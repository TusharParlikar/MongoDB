# Contributing to MongoDB Complete Mastery Course

Thank you for your interest in contributing! This document provides guidelines for contributing to this MongoDB learning resource.

## 📋 Table of Contents

1. [Code of Conduct](#code-of-conduct)
2. [Getting Started](#getting-started)
3. [How to Contribute](#how-to-contribute)
4. [Submission Guidelines](#submission-guidelines)
5. [Commit Messages](#commit-messages)
6. [Pull Request Process](#pull-request-process)

---

## Code of Conduct

### Our Pledge

We are committed to providing a welcoming and inspiring community for all. Please read and adhere to our [Code of Conduct](CODE_OF_CONDUCT.md).

### Our Standards

Examples of behavior that contribute to a positive environment:
- Using welcoming and inclusive language
- Being respectful of differing opinions
- Giving and gracefully accepting constructive feedback
- Focusing on what is best for the community
- Showing empathy towards other community members

---

## Getting Started

### Prerequisites

- Git knowledge
- MongoDB 6.0+ installed
- Node.js 18+ installed
- Familiarity with Markdown
- Understanding of MongoDB concepts

### Fork & Clone

```bash
# 1. Fork the repository on GitHub
# 2. Clone your fork
git clone https://github.com/YOUR_USERNAME/MongoDB.git

# 3. Add upstream remote
git remote add upstream https://github.com/TusharParlikar/MongoDB.git

# 4. Create a feature branch
git checkout -b feature/your-improvement
```

---

## How to Contribute

### Types of Contributions

We welcome all types of contributions:

#### 1. **Content Improvements**
- Fix typos and grammar errors
- Clarify explanations
- Update outdated information
- Add missing examples

```markdown
# Before
db.users.find({age: $gt: 18})

# After
db.users.find({ age: { $gt: 18 } })
```

#### 2. **Code Examples**
- Add new practical examples
- Fix incorrect code
- Optimize existing examples
- Add comments for clarity

```javascript
// Good example: Clear, practical, tested
const users = await User.find({ active: true })
  .select('name email')
  .limit(10);
```

#### 3. **Bug Fixes**
- Fix broken links
- Correct outdated API usage
- Fix syntax errors in code
- Update deprecated methods

#### 4. **Documentation**
- Add new sections
- Improve existing chapters
- Create better explanations
- Add visual diagrams

#### 5. **Appendix Enhancement**
- Add new commands to reference
- Expand error solutions
- Add new resources
- Improve lookup tables

---

## Submission Guidelines

### Before You Start

1. **Check existing issues** - Avoid duplicate work
2. **Open an issue first** - For major changes, discuss first
3. **Follow the style guide** - Consistency matters
4. **Test your changes** - Verify code examples work

### Content Style Guide

#### Chapters

Each chapter should follow this structure:

```markdown
# Chapter X: Topic Name

## X.1 First Section Title

Clear explanation of the concept.

### Code Example
\`\`\`javascript
// Always include working code examples
const result = await db.collection.find({});
\`\`\`

## X.2 Second Section Title

Continue with logical progression...

---

## GitHub Resources

- [Link to relevant repo](https://github.com/...)

---

Navigation: [← Previous](../chX/README.md) | [↑ Index](../../index.md) | [→ Next](../chX/README.md)
```

#### Code Examples

```javascript
// ✅ DO: Clear, commented, tested
const users = await User.find({ age: { $gt: 18 } })
  .select('-password')  // Exclude password
  .limit(10);

// ❌ DON'T: Unclear or incomplete
const u = db.u.find({a:{$gt:18}})
```

#### Links

```markdown
# ✅ Good
- [MongoDB Official Docs](https://docs.mongodb.com)

# ❌ Bad
- Check out this link: http://docs.mongodb.com
```

### Documentation Standards

- Use clear, simple language
- Avoid jargon without explanation
- Include practical examples
- Reference official documentation
- Link to GitHub repositories
- Update links if they change

---

## Commit Messages

Follow this format:

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

- `feat`: New chapter or feature
- `fix`: Bug fix or correction
- `docs`: Documentation improvement
- `style`: Formatting or style changes
- `refactor`: Code reorganization
- `perf`: Performance improvement

### Examples

```bash
# Good
git commit -m "docs(ch09): Add $in operator example with array filtering"

# Good
git commit -m "fix(appendix): Update deprecated MongoDB command syntax"

# Good
git commit -m "feat(ch30): Add complete contact form project code"

# Avoid
git commit -m "fixed stuff"
git commit -m "Updated files"
```

---

## Pull Request Process

### Before Submitting

1. **Update your branch**
```bash
git fetch upstream
git rebase upstream/main
```

2. **Test your changes**
   - Verify code examples work
   - Check links are valid
   - Proofread for typos
   - Ensure formatting is consistent

3. **Run a local check**
```bash
# Verify markdown syntax
npm run check:md

# Check for broken links (if available)
npm run check:links
```

### Submitting Your PR

1. **Push your branch**
```bash
git push origin feature/your-improvement
```

2. **Create Pull Request on GitHub**

3. **Fill out PR template**

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New content
- [ ] Improvement
- [ ] Other

## Related Issue
Fixes #(issue number)

## Changes Made
- Item 1
- Item 2

## Testing
How did you test these changes?

## Screenshots
If applicable, add screenshots

## Checklist
- [ ] Code is tested
- [ ] Links are verified
- [ ] Formatting is consistent
- [ ] No typos or grammar errors
- [ ] Follows style guide
```

### PR Review Process

1. **Initial Review** - Check formatting and structure
2. **Content Review** - Verify accuracy and clarity
3. **Testing** - Verify code examples work
4. **Approval** - Get maintainer approval
5. **Merge** - PR is merged to main branch

### Common Feedback

We may ask for:
- Additional examples
- Clearer explanations
- Fixed formatting
- Updated links
- Tested code

---

## Common Contribution Scenarios

### Scenario 1: Add a New Code Example

```markdown
# Before
## 9.1 Using $eq Operator

The $eq operator matches documents...

# After
## 9.1 Using $eq Operator

The $eq operator matches documents...

### Example: Finding Exact Matches

\`\`\`javascript
// MongoDB Shell
db.users.find({ status: { $eq: "active" } })

// With Node.js
const users = await User.find({ status: "active" });
\`\`\`

### Real-World Use Case

In an e-commerce app, find all orders with status "completed":

\`\`\`javascript
db.orders.find({ status: { $eq: "completed" } })
\`\`\`
```

### Scenario 2: Fix a Broken Link

```markdown
# Before
- Check MongoDB docs at [MongoDB](http://docs.mongodb.com/3.0)

# After
- Check MongoDB docs at [MongoDB Official Documentation](https://docs.mongodb.com)
```

### Scenario 3: Add Missing Section

1. Identify gap in content
2. Create comprehensive section
3. Include examples
4. Add related GitHub links
5. Update navigation links

---

## Approval & Merge

### What Makes a Good Contribution

✅ Clear and helpful
✅ Accurate information
✅ Well-formatted
✅ Tested code examples
✅ Follows style guide
✅ Includes documentation

### What Prevents Approval

❌ Unclear explanation
❌ Inaccurate information
❌ Poor formatting
❌ Untested code
❌ Broken links
❌ Spelling/grammar errors

---

## Recognition

Contributors will be:
1. Added to [CONTRIBUTORS.md](CONTRIBUTORS.md)
2. Thanked in release notes
3. Recognized in the README
4. Part of the community

---

## Questions?

- 💬 Ask in GitHub Discussions
- 📧 Email the maintainer
- 🐛 Open an Issue for bugs
- 💡 Open a Discussion for suggestions

---

## License

By contributing, you agree that your contributions will be licensed under the same [MIT License](LICENSE) as the project.

---

<div align="center">

**Thank you for contributing! 🙏**

Your efforts help make MongoDB learning better for everyone.

</div>
