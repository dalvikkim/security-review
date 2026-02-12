---
scope:
  domain: ["parameter-tampering", "idor"]
priority: 95
applies_to:
  - "APIs accepting user input"
  - "E-commerce and payment systems"
  - "User management endpoints"
  - "Any endpoint with object references"
---

# Parameter Tampering Security

## Overview

Parameter tampering occurs when attackers modify request parameters (query strings, form fields, cookies, headers, JSON body) to bypass security controls or access unauthorized resources.

## Entry Points

- Query string parameters (`?id=123&role=admin`)
- Form fields (hidden fields, price, quantity)
- JSON/XML request body
- Cookies and headers
- Path parameters (`/api/users/123`)

## Common Attack Scenarios

### 1. IDOR (Insecure Direct Object Reference)
- Changing `/api/orders/123` to `/api/orders/456` to access other users' orders
- Modifying `user_id` in request body to access another user's data

### 2. Privilege Escalation
- Adding `role=admin` or `isAdmin=true` to requests
- Modifying permission arrays in JSON body

### 3. Price/Quantity Manipulation
- Changing `price=100` to `price=1` in checkout
- Setting negative quantities for refunds

### 4. Hidden Field Manipulation
- Modifying hidden form fields (user ID, discount codes)
- Tampering with state parameters

### 5. Type Confusion
- Sending array where string expected: `id[]=1&id[]=2`
- Changing parameter types to bypass validation

## Secure Defaults

- Never trust client-supplied IDs for authorization decisions
- Validate ownership server-side before any operation
- Use indirect references (mapping tables) for sensitive objects
- Sign or encrypt sensitive parameters (HMAC, JWT)

## Do

- Verify object ownership on every request
- Use session-based identity, not request parameters
- Validate parameter types, ranges, and formats
- Implement rate limiting on sensitive operations
- Log parameter tampering attempts

## Don't

- Trust hidden form fields for security decisions
- Use sequential/predictable object IDs without access control
- Accept role/permission values from client
- Process price/discount from client-side calculations
- Skip validation for "internal" parameters

## High-Risk Patterns

- `getOrder(request.params.orderId)` without ownership check
- `user.role = request.body.role`
- `price = request.body.price` in payment processing
- `if (request.query.isAdmin === 'true')`
- `DELETE /api/items/{id}` without verifying user owns item
- `db.query("... WHERE id = " + req.params.id)` without authz

## Language-Specific Examples

### JavaScript/Node.js
```javascript
// VULNERABLE
app.get('/api/orders/:id', (req, res) => {
  const order = db.getOrder(req.params.id);
  res.json(order);
});

// SECURE
app.get('/api/orders/:id', (req, res) => {
  const order = db.getOrder(req.params.id);
  if (order.userId !== req.session.userId) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  res.json(order);
});
```

### Python
```python
# VULNERABLE
@app.route('/api/users/<user_id>/profile', methods=['PUT'])
def update_profile(user_id):
    data = request.json
    db.update_user(user_id, data)

# SECURE
@app.route('/api/users/profile', methods=['PUT'])
def update_profile():
    user_id = session['user_id']  # Use session identity
    data = request.json
    db.update_user(user_id, data)
```

### Java
```java
// VULNERABLE
@GetMapping("/orders/{orderId}")
public Order getOrder(@PathVariable Long orderId) {
    return orderRepository.findById(orderId);
}

// SECURE
@GetMapping("/orders/{orderId}")
public Order getOrder(@PathVariable Long orderId, Authentication auth) {
    Order order = orderRepository.findById(orderId);
    if (!order.getUserId().equals(auth.getUserId())) {
        throw new AccessDeniedException("Not authorized");
    }
    return order;
}
```

## Verification Checklist

- [ ] All object access includes ownership verification
- [ ] No client-supplied IDs used for authorization
- [ ] Price/quantity validated server-side against catalog
- [ ] Role/permission changes require admin authentication
- [ ] Hidden fields not used for security decisions
- [ ] Parameter types strictly validated
- [ ] Sequential IDs protected by access control or replaced with UUIDs
- [ ] Tampering attempts logged and monitored
