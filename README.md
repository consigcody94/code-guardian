# 🛡️ Code Guardian

**Security scanner specifically designed for AI-generated code**

Stop deploying vulnerable AI-generated code. Code Guardian detects the security patterns that AI assistants commonly introduce, providing instant feedback with actionable recommendations.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.3-blue)](https://www.typescriptlang.org/)
[![Node](https://img.shields.io/badge/Node-≥18-green)](https://nodejs.org/)

## 🔥 Why Code Guardian?

Research shows AI-generated code has:
- **322% more privilege escalation vulnerabilities**
- **153% more design flaws**
- **40% increase in exposed secrets** (hardcoded credentials, API keys)
- **60% of teams have ZERO review processes** for AI code

Code Guardian fills this critical gap with specialized detection for AI-common vulnerabilities.

## ✨ Features

### 🎯 AI-Specific Detection
- **36+ Security Patterns** covering OWASP Top 10
- **AI Code Heuristics** - detects likely AI-generated code
- **Trust Score** (0-100) for overall code security
- **Risk Level** assessment (safe → critical)

### 🔍 Vulnerability Categories
- **Secrets & Credentials** - AWS keys, GitHub tokens, API keys, passwords, JWT secrets
- **SQL Injection** - String concatenation, dynamic queries
- **XSS** - dangerouslySetInnerHTML, innerHTML, document.write
- **Command Injection** - exec(), eval(), Function constructor
- **Path Traversal** - Unsanitized file paths
- **Privilege Escalation** - Hardcoded admin checks, client-side auth
- **Insecure Deserialization** - Unsafe JSON parsing
- **Weak Cryptography** - MD5/SHA1, Math.random(), hardcoded IV/salt
- **Unsafe Regex** - ReDoS vulnerabilities

### 📊 Multiple Output Formats
- **Terminal** - Beautiful colored output with recommendations
- **JSON** - For programmatic processing
- **SARIF** - GitHub Code Scanning integration

### ⚡ Developer Experience
- **Fast** - Scans 1000s of files in seconds
- **Zero Config** - Works out of the box
- **CI/CD Ready** - Exit codes for pipeline integration
- **Detailed Reports** - File, line, column, CWE, OWASP references

## 📦 Installation

```bash
npm install -g code-guardian
```

Or use with npx:

```bash
npx code-guardian scan .
```

## 🚀 Quick Start

### Scan Current Directory
```bash
code-guardian scan .
```

### Scan Specific Files
```bash
code-guardian scan src/api src/auth
```

### Filter by Severity
```bash
code-guardian scan . --severity critical high
```

### CI/CD Integration
```bash
# Fail build on high or critical issues
code-guardian scan . --fail-on high --format json --output report.json
```

### With AI Detection
```bash
# Include AI code heuristics
code-guardian scan . --ai-heuristics
```

## 📖 Usage

### CLI Options

```
code-guardian scan [paths...]

Options:
  -e, --exclude <patterns...>     Exclude patterns
  -s, --severity <levels...>      Filter by severity (critical,high,medium,low,info)
  -c, --category <categories...>  Filter by category
  -f, --format <type>             Output format (terminal,json,sarif)
  -o, --output <file>             Output file
  --fail-on <severity>            Exit with error if issues found
  --ai-heuristics                 Include AI code detection
  --max-issues <number>           Maximum issues to report
```

### List All Patterns

```bash
code-guardian patterns
```

## 📊 Example Output

```
🛡️  Code Guardian Security Report
────────────────────────────────────────────────────────────────────────────────

┌────────────────┬────────┐
│ Metric         │ Value  │
├────────────────┼────────┤
│ Files Scanned  │ 247    │
│ Lines Scanned  │ 18,453 │
│ Duration       │ 342ms  │
│ Trust Score    │ 67/100 │
│ Risk Level     │ HIGH   │
└────────────────┴────────┘

┌──────────┬───────┐
│ Severity │ Count │
├──────────┼───────┤
│ Critical │ 3     │
│ High     │ 7     │
│ Medium   │ 12    │
│ Low      │ 5     │
│ Info     │ 2     │
│ Total    │ 29    │
└──────────┴───────┘

⚠️  Security Issues Found:

🔴 CRITICAL (3)

  src/api/auth.ts:45:12
  [secrets] AWS Access Key
  Hardcoded AWS access key detected
  💡 Use environment variables or AWS IAM roles
  ❯ const AWS_KEY = "AKIAIOSFODNN7EXAMPLE";
  CWE: CWE-798

  src/db/users.ts:23:8
  [sql-injection] SQL Concatenation
  SQL query with string concatenation (SQL injection risk)
  💡 Use parameterized queries or prepared statements
  ❯ db.query("SELECT * FROM users WHERE id = " + userId);
  CWE: CWE-89
  🤖 Likely AI-generated code

...
```

## 🔗 Integration Examples

### GitHub Actions

```yaml
name: Security Scan

on: [push, pull_request]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npx code-guardian scan . --format sarif --output results.sarif
      - uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: results.sarif
```

### Pre-commit Hook

```bash
#!/bin/sh
npx code-guardian scan $(git diff --cached --name-only --diff-filter=ACM | grep -E '\.(js|ts|jsx|tsx)$') --fail-on high
```

### Package.json Script

```json
{
  "scripts": {
    "security": "code-guardian scan src --ai-heuristics",
    "ci:security": "code-guardian scan . --fail-on high --format json"
  }
}
```

## 🎯 Common Patterns Detected

### Hardcoded Secrets (40% increase in AI code)
```javascript
// ❌ BAD - Detected by code-guardian
const API_KEY = "sk_live_abc123...";
const dbPassword = "mypassword123";

// ✅ GOOD
const API_KEY = process.env.API_KEY;
```

### SQL Injection (Common in AI scaffolding)
```javascript
// ❌ BAD - Detected by code-guardian
db.query(`SELECT * FROM users WHERE id = ${userId}`);

// ✅ GOOD
db.query('SELECT * FROM users WHERE id = ?', [userId]);
```

### Privilege Escalation (322% more in AI code)
```javascript
// ❌ BAD - Detected by code-guardian
if (user.role === 'admin') { /* client-side check */ }

// ✅ GOOD
// Server-side verification with proper RBAC
```

### XSS Vulnerabilities
```javascript
// ❌ BAD - Detected by code-guardian
<div dangerouslySetInnerHTML={{__html: userInput}} />

// ✅ GOOD
import DOMPurify from 'dompurify';
<div dangerouslySetInnerHTML={{__html: DOMPurify.sanitize(userInput)}} />
```

## 🏗️ Programmatic Usage

```typescript
import { SecurityScanner } from 'code-guardian';

const scanner = new SecurityScanner();
const result = await scanner.scan({
  paths: ['./src'],
  severity: ['critical', 'high'],
  includeAiHeuristics: true,
});

console.log(`Trust Score: ${result.trustScore}/100`);
console.log(`Risk Level: ${result.riskLevel}`);
console.log(`Issues: ${result.summary.total}`);
```

## 📚 Why AI Code Needs Special Scanning

AI assistants like GitHub Copilot, ChatGPT, and Claude are incredibly powerful but have predictable blind spots:

1. **Over-scaffolding** - AI generates complete examples with hardcoded credentials
2. **Pattern copying** - AI learns from insecure code examples online
3. **Missing context** - AI doesn't know your security requirements
4. **Boilerplate vulnerability** - Common patterns (auth checks, SQL queries) often insecure
5. **No security review** - 60% of teams don't review AI code before deploying

Code Guardian bridges this gap with specialized patterns and AI detection heuristics.

## 🎓 Security References

- **CWE** - Common Weakness Enumeration mappings
- **OWASP Top 10** - Industry-standard vulnerability categories
- **Research-backed** - Patterns based on real AI code vulnerability studies

## 🛠️ Development

```bash
# Clone repository
git clone https://github.com/consigcody94/code-guardian.git
cd code-guardian

# Install dependencies
npm install

# Build
npm run build

# Run locally
npm link
code-guardian scan .
```

## 📄 License

MIT - see [LICENSE](LICENSE)

## 🤝 Contributing

Contributions welcome! See [CONTRIBUTING.md](CONTRIBUTING.md)

## 📈 Roadmap

- [ ] Machine learning for custom pattern detection
- [ ] IDE extensions (VS Code, JetBrains)
- [ ] Real-time scanning during development
- [ ] Custom pattern configuration
- [ ] Severity customization
- [ ] Baseline comparison (track improvements over time)
- [ ] Team dashboards
- [ ] Integration with more SAST tools

## ⭐ Star History

If Code Guardian helps you ship more secure code, give it a star! ⭐

---

**Built for developers who use AI tools and want to ship secure code.**

Stop the 322% privilege escalation increase. Scan your AI-generated code today.
