[Go back to PortSwigger Index](index)

# CSRF


# Bypassing CSRF Token Validation

1. CSRF vulnerability without any defense
2. Validation of CSRF request depends on request method
3. Validation of CSRF token depends on token being present
4. CSRF is not tied to the user session
5. CSRF token is tied to a non-session cookie
6. CSRF token is simply duplicated in a cookie

# Bypassing SameSite cookie restrictions

1. Bypassing SameSite Lax Restrictions using GET requests
2. Bypassing SameSite Strict Restrictions using on-site gadgets
3. Bypassing SameSite restrictions via vulnerable sibling domains
4. Bypassing SameSite Lax restrictions with newly issued cookies

# Bypassing Referer-Based CSRF Defenses

1. Changing the referer header with the HTML meta tag
2. Changing the Attacker website URL
