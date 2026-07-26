# Contributing to Flood Sentinel

Thank you for your interest in contributing to Flood Sentinel! We are building this research initiative in the open, and we welcome contributions from researchers, engineers, students, and domain experts.

## Our Community

Flood Sentinel is a community-driven research project. We believe that:
- Every perspective adds value
- Diverse expertise strengthens our approach
- Open collaboration leads to better solutions
- Transparency is essential for research integrity

## Ways to Contribute

### 🔬 Research & Knowledge
- Conduct experiments and share findings
- Review and validate existing research
- Propose new research directions
- Share domain expertise and insights
- Write papers or technical analyses
- Contribute datasets from field studies

### 💻 Software Development
- Write and improve application code
- Develop prediction models and algorithms
- Create dashboards and visualizations
- Build APIs and integrations
- Improve code quality and documentation
- Fix bugs and optimize performance

### 🔧 Hardware & IoT
- Design and test sensor prototypes
- Create PCB designs and schematics
- Develop firmware for IoT devices
- Build bills of materials (BOM)
- Document hardware assembly procedures
- Test sensor accuracy and reliability

### 📚 Documentation
- Write guides and tutorials
- Improve existing documentation
- Translate content to other languages
- Create diagrams and visualizations
- Document setup and deployment procedures
- Share case studies and use cases

### ✅ Testing & Quality
- Test features and report bugs
- Validate research findings
- Review pull requests
- Contribute test data
- Perform field validation
- Document edge cases

### 📢 Community & Outreach
- Answer questions in discussions
- Mentor newcomers
- Promote the project
- Organize events or workshops
- Connect with relevant communities
- Provide feedback on direction

## Getting Started

### 1. Read Our Documentation
- [README.md](../README.md) - Project overview
- [CODE_OF_CONDUCT.md](../CODE_OF_CONDUCT.md) - Community guidelines
- [Roadmap.md](../docs/Roadmap.md) - Current phase and milestones
- [Architecture.md](../docs/Architecture.md) - System design

### 2. Set Up Your Environment
Clone the repository:
```bash
git clone https://github.com/erhardik/flood-sentinel.git
cd flood-sentinel
```

### 3. Find Something to Work On
- Browse [open issues](https://github.com/erhardik/flood-sentinel/issues)
- Check [research proposals](https://github.com/erhardik/flood-sentinel/issues?q=label%3Aresearch)
- Look at [Roadmap.md](../docs/Roadmap.md) for planned work
- Start a [discussion](https://github.com/erhardik/flood-sentinel/discussions) with your ideas

### 4. Discuss Before You Commit
For significant work:
- Open an issue or discussion first
- Describe your proposal and approach
- Wait for feedback from maintainers
- This prevents duplicate work and ensures alignment

## Contribution Guidelines

### Issue Submission

#### Bug Reports
Use the [Bug Report](../.github/ISSUE_TEMPLATE/bug_report.md) template. Include:
- Clear description of the bug
- Steps to reproduce
- Expected vs. actual behavior
- Environment details (OS, Python version, etc.)
- Screenshots or error logs if relevant

#### Feature Requests
Use the [Feature Request](../.github/ISSUE_TEMPLATE/feature_request.md) template. Include:
- Problem statement and use case
- Proposed solution
- Alternatives considered
- Impact and priority assessment

#### Research Proposals
Use the [Research Proposal](../.github/ISSUE_TEMPLATE/research_proposal.md) template. Include:
- Research question or hypothesis
- Methodology and approach
- Expected outcomes and deliverables
- Timeline and resource requirements
- Relevant citations and background

### Pull Request Process

1. **Fork the repository** and create a feature branch:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes** following the guidelines below:
   - Keep commits atomic and well-documented
   - One logical change per commit
   - Write clear, concise commit messages
   - Reference related issues in commit messages

3. **Test your changes**:
   - Run existing tests
   - Add new tests for new functionality
   - Verify documentation is accurate
   - Test in multiple environments if applicable

4. **Update documentation**:
   - Update README.md if behavior changes
   - Add docstrings to functions and classes
   - Update relevant docs in the `docs/` directory
   - Include usage examples where appropriate

5. **Submit a pull request**:
   - Use the [Pull Request template](../.github/pull_request_template.md)
   - Link related issues
   - Describe your changes and motivation
   - Explain any design decisions
   - Request review from relevant maintainers

6. **Address feedback**:
   - Respond to review comments
   - Make requested changes
   - Push updates to your branch
   - Re-request review when ready

### Code Standards

#### Python
- Follow [PEP 8](https://pep8.org/) style guide
- Use type hints where possible
- Write docstrings for all functions and classes
- Keep functions focused and testable
- Aim for 80% test coverage or higher

#### Hardware/Firmware
- Document design decisions in comments
- Provide PCB schematics and layouts
- Include parts lists and sourcing information
- Test across target hardware platforms
- Document calibration procedures

#### Documentation
- Use clear, accessible language
- Include examples and diagrams
- Keep links and references up-to-date
- Use consistent formatting and structure
- Proofread before submitting

### Commit Message Guidelines

Write clear, descriptive commit messages:

```
Type: Brief description (50 chars max)

Longer explanation if needed. Explain what changed and why.
Keep lines under 72 characters.

Fixes #123
Related to #456
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`

### Research Contribution Guidelines

For research contributions:
- Clearly state hypothesis and methodology
- Document data sources and preparation
- Include reproducibility information (code, data, parameters)
- Share results transparently (including negative findings)
- Cite relevant papers and prior work
- Make datasets available (when privacy permits)
- Consider peer review for major findings

## Licensing

By contributing to Flood Sentinel, you agree that your contributions will be licensed under the [MIT License](../LICENSE). This ensures the project remains open and accessible.

## Review Process

All contributions go through a review process:

1. **Automated checks**: CI/CD pipeline runs tests and linting
2. **Code review**: Maintainers review for quality and alignment
3. **Community feedback**: Others may comment with suggestions
4. **Approval**: Maintainers approve and merge when ready

Reviews may take time—we thank you for your patience. We aim to provide constructive, respectful feedback.

## Getting Help

- **Questions?** Open a [discussion](https://github.com/erhardik/flood-sentinel/discussions)
- **Getting stuck?** Comment on relevant issues or create a new discussion
- **Need guidance?** Mention `@erhardik` or relevant experts in comments
- **Reporting problems?** Submit a bug report with full details

## Recognition

We value all contributions. Contributors will be:
- Listed in our CONTRIBUTORS file
- Mentioned in release notes for their contributions
- Credited in relevant documentation
- Recognized in community communications

## Code of Conduct

Please note that this project is governed by our [Code of Conduct](../CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code.

## Questions or Suggestions?

Have ideas to improve our contribution process? We'd love to hear from you! Open an issue or discussion to share feedback.

---

**Thank you for helping build Flood Sentinel!** 🌊
