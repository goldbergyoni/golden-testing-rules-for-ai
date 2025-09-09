# 💎 Golden Testing Rules for AI

## Why is it critical?
As coding shifts toward AI agents, test quality becomes 100x more critical. In 60 seconds, shield your tests from 50+ common mistakes that AI coding tools make

Download one rule file today and watch your project naturally shift toward more reliable, maintainable testing

### 🎯 What You'll Find Here

1. **📚 Battle-tested wisdom from 20+ years of testing literature** - Curated best practices from the most influential testing books of the last two decades
2. **🤖 AI-optimized format** - Specifically formatted for Claude, Cursor, Copilot, and other AI coding assistants to understand and apply
3. **⚡ Context-window friendly** - Smartly compacted rules that maximize value while minimizing token usage

## 🚀 Get Started in 30 Seconds

Download the testing rules and add them to your AI tool:

```bash
# Download the testing best practices
curl -O https://raw.githubusercontent.com/goldbergyoni/golden-testing-rules-for-ai/main/testing-best-practices.md
```

Or [view and copy the rules directly from GitHub](https://github.com/goldbergyoni/golden-testing-rules-for-ai/blob/main/testing-best-practices.md)

### Integration with your AI tool:

**For Claude Code:**
- Rename the file to `CLAUDE.md` and place it under your testing folder or wherever you use to locate testing best practices

**For Cursor:**
- Paste the contents into a `.cursorrules` file in your project root

> **Note:** Currently available for TypeScript/JavaScript. Support for more languages and tool-specific formats coming soon!

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
test('When processing order items, then total reflects sum plus tax', async () => {
  // Arrange - all data visible (🔫 Smoking gun principle)
  const order1 = buildOrderItem({ price: 50, quantity: 1 })
  const order2 = buildOrderItem({ price: 25, quantity: 1 })
  
  // Act
  const result = await httpClient.post('/order/process', [order1, order2])
  
  // Assert - clear cause and effect
  expect(result.total).toBe((order1.price + order2.price) * config.taxRate)
})
```

## 📚 Standing on the Shoulders of Giants

Our rules are distilled from decades of testing wisdom:

### **xUnit Test Patterns** by Gerard Meszaros
<img src="https://m.media-amazon.com/images/I/51aohHQXSmL._SY445_SX342_.jpg" width="100" alt="xUnit Test Patterns">

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

A comprehensive guide with 50+ best practices specifically for JavaScript testing, battle-tested by thousands of developers. [25,000+ ⭐ on GitHub](https://github.com/goldbergyoni/javascript-testing-best-practices)

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

## 👨‍💻 About the Author

**Yoni Goldberg** - Developer and consultant with extensive experience in testing excellence. Has worked with over 40 organizations to improve their testing practices. Author of Node.js and JavaScript testing best practices repositories with a combined 130,000+ GitHub stars 🌟, helping developers worldwide write better, more maintainable tests.

---

*Making AI coding assistants write tests like seasoned testing experts, one rule at a time.*