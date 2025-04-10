---
icon: material/arrow-right
---

# Dependency Injection in PHP

Ever found yourself writing a class, and inside one of its methods, you suddenly need functionality from *another* class? The quick solution is often to just create an instance of that other class right there: `$helper = new HelperClass();`. While easy, this seemingly small decision can lead to headaches down the road, making your code rigid and hard to test.

There's a better way: **Dependency Injection (DI)**.

## The Problem: Hidden Dependencies & Tight Coupling

Imagine our `PaymentGateWay` class needs a `ChargeBee` service to process payments. A naive approach *without* DI might look like this internally:

```php title="PaymentGateWay_Bad.php (Hard-coded Dependency)"
<?php
// A less flexible approach (NOT using DI effectively)
class ChargeBee {
    // ... Chargebee methods ...
    public function payNow() { echo "Processing via Chargebee...\n"; }
}

class PaymentGateWay_Bad {
    protected $gateway;

    public function __construct() {
        // Problem: PaymentGateWay *creates* its own dependency.
        // It's now permanently tied to ChargeBee.
        $this->gateway = new ChargeBee(); // Hard-coded dependency!
        echo "Payment Gateway (Bad) initialised, forced to use Chargebee.\n";
    }

    public function charge() {
        $this->gateway->payNow();
    }
}

// Usage
$badGateway = new PaymentGateWay_Bad();
$badGateway->charge(); // Always uses Chargebee
?>
```

__What's wrong here?__

1.  **Inflexibility:** If you want `PaymentGateWay_Bad` to use Stripe instead, you *must* modify the `PaymentGateWay_Bad` class itself. This violates the Open/Closed Principle.
2.  **Hidden Dependencies:** Looking only at the constructor signature `__construct()`, you have no idea it secretly needs and creates a `ChargeBee` object. The class's requirements aren't explicit.
3.  **Testing Nightmare:** How do you test `PaymentGateWay_Bad` in isolation without involving a *real* `ChargeBee` object (which might make actual API calls)? It's very difficult because the dependency creation is hard-coded inside.

## The Solution Part 1: Dependency Injection (DI)

Dependency Injection flips this around. Instead of a class sneakily creating its own tools (dependencies), we **give** (or "inject") those tools to the class when it's created.

!!! note "Dependency Injection (DI)"
    The core idea is to provide the necessary dependencies (other objects a class needs to function) to an object from an *external* source, rather than having the object create them internally.

Let's refactor our `PaymentGateWay` using DI, specifically **Constructor Injection**:

```php title="ChargeBee.php (Example Dependency)"
<?php
// Assume this class exists and provides the needed functionality
class ChargeBee {
    public function payNow() { echo "Processing via Chargebee...\n"; }
}
?>
```

```php title="PaymentGateWay_Better.php (Constructor Injection)"
<?php
class PaymentGateWay_Better {
    protected $gateway; // Still holds the payment service

    // The FIX: We now ASK for the dependency in the constructor signature.
    // The dependency (ChargeBee object) is "injected" from outside.
    // Type hinting (ChargeBee) makes the requirement explicit.
    public function __construct(ChargeBee $chargeBeeService) {
        $this->gateway = $chargeBeeService; // We just store what we were GIVEN
        echo "Payment Gateway (Better) initialised, using the provided service.\n";
    }

    public function charge() {
        $this->gateway->payNow(); // Uses the injected service
    }
}
?>
```

```php title="Usage Example (Injecting the Dependency)"
<?php
// --- How we use it NOW ---
echo "Setting up the Better Payment Gateway:\n";

// Step 1: Create the dependency *outside* the consuming class
$chargeBee = new ChargeBee();

// Step 2: Inject the dependency when creating PaymentGateWay_Better
$betterGateway = new PaymentGateWay_Better($chargeBee);

echo "\nProcessing a charge:\n";
$betterGateway->charge(); // Still uses Chargebee, but setup is decoupled
?>
```

!!! success "What did DI fix?"
    *   **Explicit Dependencies:** The constructor `__construct(ChargeBee $chargeBeeService)` clearly signals "I need a `ChargeBee` object to work!". It's upfront about its requirements, improving code clarity and discoverability.
    *   **Improved Testability:** We can now easily provide a *mock* or *fake* `ChargeBee` object during testing. Since the `PaymentGateWay_Better` accepts any object matching the `ChargeBee` type hint (or, ideally, an interface - more on that later), we can inject a test double that simulates `ChargeBee` behavior without hitting external services.
    *   **Increased Flexibility (Decoupling):** While still dependent on the *type* `ChargeBee`, the `PaymentGateWay_Better` is no longer responsible for *creating* its dependency. This separation of concerns makes the class more focused and reusable.

!!! tip "Think of it like building a car:"
    *   **Without DI:** The car factory tries to build its own engine from scratch inside the car assembly line. Hard to swap engines or test the car chassis independently!
    *   **With DI:** The engine is built separately (or sourced) and *delivered* (injected) into the car during assembly. Much easier to use different engines (dependency implementations) or test the car with a dummy engine (mock object).

## Next Steps: Towards True Decoupling

Dependency Injection, especially Constructor Injection, is a huge step forward. It promotes better design, testability, and maintainability by making dependencies explicit and externalizing their creation.

However, notice our `PaymentGateWay_Better` *still demands a specific concrete class*: `ChargeBee`. We've improved visibility and testability, but we still have **tight coupling** to that specific implementation. What if we want to seamlessly swap `ChargeBee` for `Stripe` *without* changing `PaymentGateWay_Better` at all?

This is where **programming to interfaces** and the **Inversion of Control (IoC)** principle come into play. Stay tuned as we explore how to achieve truly flexible and decoupled components!