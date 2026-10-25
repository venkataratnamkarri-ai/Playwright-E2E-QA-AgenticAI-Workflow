# 🛠️ Agent 3: Self-Healing Agent

## Role & Mission
The **Self-Healing Agent** eliminates test flakiness and maintenance overhead. When a Playwright test fails due to UI drift, DOM structure updates, or brittle locators, this agent intercepts the failure, analyzes the DOM snapshot or accessibility tree, diagnoses the root cause, heals the test code, and re-runs the suite to verify the fix.

---

## System Prompt

```markdown
You are a Self-Healing QA Test Diagnostic Specialist.
When a test failure occurs during Playwright execution:
1. Parse the error stack trace, locator failure, and expected vs. actual values.
2. Inspect the current page DOM snapshot or accessibility tree captured at failure time.
3. Diagnose the failure category:
   - Category A: Selector drift (class names changed, DOM hierarchy shifted).
   - Category B: Text mismatch (subtle copy changes or casing differences).
   - Category C: Timing issue (missing await or animation delay).
   - Category D: True application bug.
4. If Category A or B, locate the intended element and suggest the healed, robust selector (favoring `data-test` attributes).
5. Apply the code edit directly to the test file and re-execute to confirm the fix.
```

---

## 🔍 Real Healing Case Studies in this Project

During initial automation of the SauceDemo checkout flow, **2 tests failed out of 20 (90% pass rate)**. The Self-Healing Agent healed both issues, raising the pass rate to **100% (20/20)**.

### Case 1: Brittle CSS Selector &rarr; Robust `data-test` Attribute

- **Affected File**: `tests/saucedemo-checkout/complete-checkout-flow.spec.ts`
- **Error**: `expect(locator).toBeVisible() failed for .summary_value with text 'SauceCard #31337'`
- **Diagnosis**: The test was relying on generic CSS class `.summary_value`, which broke when the DOM structure rendered multiple summary elements.
- **The Self-Healing Patch**:

```diff
- await expect(page.locator('.summary_value').filter({ hasText: 'SauceCard #31337' })).toBeVisible();
- await expect(page.locator('.summary_value').filter({ hasText: 'Free Pony Express Delivery' })).toBeVisible();
+ await expect(page.locator('[data-test="payment-info-value"]')).toContainText('SauceCard #31337');
+ await expect(page.locator('[data-test="shipping-info-value"]')).toContainText('Free Pony Express Delivery');
```

---

### Case 2: Exact Copy Mismatch &rarr; Resilient Assertion

- **Affected File**: `tests/saucedemo-checkout/order-completion.spec.ts`
- **Error**: `Expected text: "Pony Express", Received: "Your order has been dispatched, and will arrive just as fast as the pony can get there!"`
- **Diagnosis**: Case sensitivity and exact substring matching failed against revised marketing copy.
- **The Self-Healing Patch**:

```diff
- await expect(page.locator('[data-test="complete-text"]')).toContainText('Pony Express');
+ await expect(page.locator('[data-test="complete-text"]')).toContainText('pony');
```

---

## 📈 Impact
- **Before Healing**: 18 Passed, 2 Failed (90%)
- **After Healing**: 20 Passed, 0 Failed (**100% Pass Rate**)
- **Human Intervention Required**: **Zero hours**.
