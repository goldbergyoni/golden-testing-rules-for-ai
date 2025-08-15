# Golden Testing Rules for AI 🏆

## Why is it critical?
As coding shifts toward AI agents, test quality becomes 100x more critical. In 60 seconds, shield your tests from 50+ common mistakes that AI coding tools make

Download one rule file today and watch your project naturally shift toward more reliable, maintainable testing

### 🎯 What You'll Find Here

1. **📚 Battle-tested wisdom from 20+ years of testing literature** - Curated best practices from the most influential testing books of the last two decades
2. **🤖 AI-optimized format** - Specifically formatted for Claude, Cursor, Copilot, and other AI coding assistants to understand and apply
3. **⚡ Context-window friendly** - Smartly compacted rules that maximize value while minimizing token usage

## 🚀 Get Started in 30 Seconds

Choose your AI tool and add the rules to your project:

```bash
# For Claude (Claude Code, claude.ai)
curl -O https://raw.githubusercontent.com/goldbergyoni/golden-testing-rules-for-ai/main/CLAUDE.md
mv CLAUDE.md your-project/tests/

# For Cursor
curl -O https://raw.githubusercontent.com/goldbergyoni/golden-testing-rules-for-ai/main/.cursorrules
mv .cursorrules your-project/

# For GitHub Copilot
curl -O https://raw.githubusercontent.com/goldbergyoni/golden-testing-rules-for-ai/main/.github/copilot-instructions.md
mkdir -p your-project/.github && mv copilot-instructions.md your-project/.github/
```

That's it! Your AI assistant now follows golden testing standards. 🛡️

## 📖 Example: Spot the Difference

### ❌ Typical AI-Generated Test (3 Critical Issues)
```typescript
// BAD: Vague title, no clear scenario
test('should work correctly', async () => {
  // Missing Arrange phase - no data setup visible
  
  // Implementation detail testing
  const service = new OrderService()
  expect(service.internalCache).toBeDefined()
  
  // Arbitrary timeout - flaky test waiting
  await page.waitForTimeout(2000)
  
  const result = await service.processOrder()
  
  // Missing smoking gun - where's the test data?
  expect(result.total).toBe(150)
})
```

### ✅ With Golden Testing Rules Applied
```typescript
test('When processing order with 2 items, then total reflects sum plus tax', async () => {
  // Arrange - all data visible (🔫 Smoking gun principle)
  const orderItems = [
    buildOrderItem({ price: 50, quantity: 2 }),
    buildOrderItem({ price: 25, quantity: 2 })
  ]
  const order = buildOrder({ items: orderItems, taxRate: 0.1 })
  
  // Act
  const result = await processOrder(order)
  
  // Assert - clear cause and effect
  const expectedTotal = (100 + 50) * 1.1  // sum * tax
  expect(result.total).toBe(expectedTotal)
})
```

## 📚 Standing on the Shoulders of Giants

Our rules are distilled from decades of testing wisdom:

### **xUnit Test Patterns** by Gerard Meszaros
<img src="https://m.media-amazon.com/images/I/51qaIf1qE0L.jpg" width="100" alt="xUnit Test Patterns">

The definitive catalog of test patterns and anti-patterns. Meszaros identifies 400+ patterns that form the foundation of modern testing practices.

### **Growing Object-Oriented Software, Guided by Tests** by Freeman & Pryce
<img src="https://m.media-amazon.com/images/I/51Apk1MuaXL.jpg" width="100" alt="GOOS">

The GOOS book pioneered the London School of TDD and introduced the concept of testing from the outside-in, focusing on behavior over implementation.

### **Test Driven Development: By Example** by Kent Beck
<img src="https://m.media-amazon.com/images/I/51ECVL7KkBL.jpg" width="100" alt="TDD by Example">

Beck's seminal work introduced TDD to the world, establishing the Red-Green-Refactor cycle and the principle of writing tests first.

### **The Art of Unit Testing** by Roy Osherove
<img src="https://m.media-amazon.com/images/I/71Ww3b1TmJL.jpg" width="100" alt="Art of Unit Testing">

Osherove's practical guide teaches the difference between good and bad tests, emphasizing maintainability, readability, and trustworthiness.

### **JavaScript Testing Best Practices** by Yoni Goldberg
<img src="https://github.com/goldbergyoni/javascript-testing-best-practices/raw/master/assets/jtbp-header-blue.png" width="300" alt="JavaScript Testing Best Practices">

A comprehensive guide with 50+ best practices specifically for JavaScript testing, battle-tested by thousands of developers. [5,000+ ⭐ on GitHub](https://github.com/goldbergyoni/javascript-testing-best-practices)

## 🎓 Why This Matters

AI coding assistants are powerful but often generate tests with subtle issues:
- **Hidden dependencies** that make tests fragile
- **Implementation testing** instead of behavior testing  
- **Missing test data** that makes failures hard to debug
- **Arbitrary waits** that create flaky tests
- **Poor structure** that hurts maintainability

Our rules teach AI to avoid these pitfalls automatically.

## 🤝 Contributing

Found a testing anti-pattern that AI tools keep generating? Open an issue or PR! We're building the definitive resource for AI-powered testing excellence.

## 📜 License

MIT - Use these rules freely in your projects

---

*Making AI coding assistants write tests like seasoned testing experts, one rule at a time.*