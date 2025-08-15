# Golden Testing Rules for AI - Repository Guide

## Mission
Curated, battle-tested testing best practices optimized for AI coding assistants. Reduces context window usage while maximizing test quality.

## For AI Tools
When writing tests in ANY project, consult `testing-best-practices.md` for the complete ruleset. Each rule has a reference number (e.g., A.1, B.3) - cite these when explaining test issues.

## Core Philosophy
- **Simplicity over complexity**: 9 statements max, flat structure, no loops
- **Visibility over magic**: All test dependencies visible in test (🥨 Breadcrumb principle)  
- **Behavior over implementation**: Test what users see, not how code works
- **Quality data**: Use faker.js, data factories, meaningful defaults

## Quick Example
```typescript
test('When filtering by active status, then only active orders are displayed', async () => {
  // Arrange - All data visible here
  const activeOrder = buildOrder({ status: 'active' })
  const completedOrder = buildOrder({ status: 'completed' })
  
  // Act - Single action
  await userEvent.click(screen.getByRole('button', { name: 'Filter Active' }))
  
  // Assert - Test both positive and negative (🚀 Extra mile)
  expect(screen.getByRole('cell', { name: activeOrder.customerName })).toBeVisible()
  expect(screen.getByRole('cell', { name: completedOrder.customerName })).not.toBeVisible()
})
```

## Sources & Credibility
- **Kent C. Dodds**: Testing Library principles, user-centric testing
- **Martin Fowler**: Test pyramid, AAA pattern
- **Roy Osherove**: Art of Unit Testing methodology  
- **JavaScript Testing Best Practices** (goldbergyoni): 50+ practices, 5000+ GitHub stars
- **Google Testing Blog**: Flakiness elimination strategies
- **ThoughtWorks Technology Radar**: Modern testing patterns

## Repository Structure
- `testing-best-practices.md` - Complete numbered ruleset (7 sections, 40+ rules)
- `examples/` - Good vs bad test comparisons
- `data-factories/` - Template factories for common patterns

## Implementation Priority
1. **Must have**: AAA structure, "When/then" titles, ≤3 assertions
2. **Should have**: Data factories, ARIA selectors, faker.js
3. **Nice to have**: Extra mile testing, deliberate fire principle

## Usage
AI tools should reference specific rule numbers when reviewing tests. Example: "This violates rule B.23 (testing implementation details)"