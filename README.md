# EX-NO-11-ELLIPTIC-CURVE-CRYPTOGRAPHY-ECC

## Aim:
To Implement ELLIPTIC CURVE CRYPTOGRAPHY(ECC)


## ALGORITHM:

1. Elliptic Curve Cryptography (ECC) is a public-key cryptography technique based on the algebraic structure of elliptic curves over finite fields.

2. Initialization:
   - Select an elliptic curve equation \( y^2 = x^3 + ax + b \) with parameters \( a \) and \( b \), along with a large prime \( p \) (defining the finite field).
   - Choose a base point \( G \) on the curve, which will be used for generating public keys.

3. Key Generation:
   - Each party selects a private key \( d \) (a random integer).
   - Calculate the public key as \( Q = d \times G \) (using elliptic curve point multiplication).

4. Encryption and Decryption:
   - Encryption: The sender uses the recipient’s public key and the base point \( G \) to encode the message.
   - Decryption: The recipient uses their private key to decode the message and retrieve the original plaintext.

5. Security: ECC’s security relies on the Elliptic Curve Discrete Logarithm Problem (ECDLP), making it highly secure with shorter key lengths compared to traditional methods like RSA.

## Program:
```
class Point:
    """Represents a point on an elliptic curve."""
    def __init__(self, x=0, y=0, is_infinity=False):
        self.x = x
        self.y = y
        self.is_infinity = is_infinity

    def __repr__(self):
        if self.is_infinity:
            return "Point at Infinity"
        return f"({self.x}, {self.y})"


# Elliptic curve parameters for y^2 = x^3 + ax + b over F_p
a = 2  # Coefficient a
b = 3  # Coefficient b
p = 17 # Prime number for the finite field F_p


def mod_inverse(k: int, p: int) -> int:
    """Calculates modular inverse of k modulo p (k^-1 mod p)."""
    k = k % p
    for x in range(1, p):
        if (k * x) % p == 1:
            return x
    return -1  # No inverse exists


def point_addition(P: Point, Q: Point) -> Point:
    """Adds two points on the elliptic curve over F_p."""
    if P.is_infinity:
        return Q
    if Q.is_infinity:
        return P

    # If P == -Q (additive inverse)
    if P.x == Q.x and (P.y + Q.y) % p == 0 and P.y != 0:
        return Point(is_infinity=True)

    if P.x == Q.x and P.y == Q.y:
        # Point Doubling
        denominator = mod_inverse(2 * P.y, p)
        if denominator == -1:
            return Point(is_infinity=True)
        lambda_val = ((3 * P.x * P.x + a) * denominator) % p
    else:
        # Point Addition
        denominator = mod_inverse(Q.x - P.x, p)
        if denominator == -1:
            return Point(is_infinity=True)
        lambda_val = ((Q.y - P.y) * denominator) % p

    rx = (lambda_val * lambda_val - P.x - Q.x) % p
    ry = (lambda_val * (P.x - rx) - P.y) % p

    return Point(rx, ry, is_infinity=False)


def scalar_multiplication(P: Point, n: int) -> Point:
    """Performs scalar multiplication (n * P) using double-and-add strategy."""
    result = Point(is_infinity=True)  # Start with point at infinity
    addend = P

    while n > 0:
        if n & 1:
            result = point_addition(result, addend)
        addend = point_addition(addend, addend)
        n >>= 1

    return result


if __name__ == "__main__":
    G = Point(5, 1)  # Base point on the curve
    n = 7            # Scalar multiplier

    print(f"Base point G: {G}")
    R = scalar_multiplication(G, n)

    if R.is_infinity:
        print(f"Result of {n} * G: Point at Infinity")
    else:
        print(f"Result of {n} * G: {R}")
```


## Output:

<img width="667" height="351" alt="image" src="https://github.com/user-attachments/assets/9f59ec69-031d-459c-8a11-6ade92876d68" />

## Result:
The program is executed successfully

