# SonarQube Setup and Configuration

## Overview

This document explains how to set up and use SonarQube for code quality analysis in the DevGate project.

## Quick Start with Docker

### 1. Start SonarQube Server

```bash
# Start SonarQube with PostgreSQL database
docker-compose up -d sonarqube postgres

# Wait for SonarQube to start (usually takes 2-3 minutes)
# Check logs: docker-compose logs -f sonarqube
```

### 2. Initial Setup

1. Open http://localhost:9000
2. Default credentials: `admin/admin`
3. Change the default password when prompted
4. Create a new project with key: `DevGate`
5. Generate a project token for analysis

### 3. Run Analysis

```bash
# Install sonar-scanner globally (if not already installed)
npm install -g sonarqube-scanner

# Set your project token
export SONAR_TOKEN=your_generated_token

# Run analysis
npm run sonar:local
```

## Configuration Files

### sonar-project.properties

The project is configured with the following settings:

- **Project Key**: `DevGate`
- **Source Directory**: `src/`
- **Exclusions**: Excludes `node_modules`, `dist`, `coverage`, `public`, and test files
- **Language Support**: JavaScript, TypeScript, and Vue.js files
- **Coverage**: Configured for LCOV reports

### GitHub Actions Integration

The `.github/workflows/build.yml` file includes:

- Automated SonarQube analysis on push to main branch
- Pull request analysis
- Quality gate checks
- Node.js environment setup

## Scripts Available

```bash
# Local analysis with default SonarQube server
npm run sonar

# Local analysis with local Docker SonarQube
npm run sonar:local
```

## Production Setup

### GitHub Secrets Required

Set these secrets in your GitHub repository:

1. `SONAR_TOKEN`: Your SonarQube project token
2. `SONAR_HOST_URL`: Your SonarQube server URL (e.g., `https://sonarcloud.io` or your self-hosted URL)

### SonarCloud Integration

For SonarCloud.io (recommended for open source):

1. Go to https://sonarcloud.io
2. Sign in with your GitHub account
3. Import your repository
4. Get your project key and token
5. Update `sonar-project.properties` if needed
6. Set GitHub secrets

## Local Development

### Prerequisites

```bash
# Install SonarQube Scanner
npm install -g sonarqube-scanner

# Or install locally in project
npm install --save-dev sonarqube-scanner
```

### Running Local Analysis

1. Start the local SonarQube server:
   ```bash
   docker-compose up -d sonarqube postgres
   ```

2. Wait for SonarQube to be ready (check http://localhost:9000)

3. Create a project in SonarQube UI or use existing configuration

4. Set your token:
   ```bash
   # Windows PowerShell
   $env:SONAR_TOKEN="your_token_here"
   
   # Windows CMD
   set SONAR_TOKEN=your_token_here
   
   # Linux/Mac
   export SONAR_TOKEN=your_token_here
   ```

5. Run analysis:
   ```bash
   npm run sonar:local
   ```

## Quality Gates

The project is configured with quality gates that will:

- Fail the build if critical issues are found
- Check for code coverage thresholds
- Verify security hotspots are reviewed
- Ensure maintainability rating standards

## Troubleshooting

### Common Issues

1. **SonarQube not starting**:
   - Check Docker logs: `docker-compose logs sonarqube`
   - Ensure ports 9000 and 5432 are available
   - Wait longer for initialization (can take 3-5 minutes)

2. **Analysis fails**:
   - Verify SONAR_TOKEN is set correctly
   - Check network connectivity to SonarQube server
   - Ensure sonar-scanner is installed

3. **Quality gate failing**:
   - Review issues in SonarQube dashboard
   - Fix critical and major issues first
   - Check coverage reports if tests are failing

### Logs and Debugging

```bash
# Check SonarQube server logs
docker-compose logs sonarqube

# Check PostgreSQL logs
docker-compose logs postgres

# Run scanner with debug output
sonar-scanner -Dsonar.verbose=true
```

## Best Practices

1. **Regular Analysis**: Run analysis on every commit to main branch
2. **Pull Request Analysis**: Analyze PRs before merging
3. **Fix Issues Early**: Address new issues immediately
4. **Coverage Goals**: Maintain good test coverage
5. **Security**: Review security hotspots promptly

## Integration with IDE

### VS Code

Install the SonarLint extension for real-time code analysis:

1. Install "SonarLint" extension
2. Connect to your SonarQube server
3. Get real-time feedback while coding

### Other IDEs

SonarLint is available for:
- IntelliJ IDEA
- Eclipse
- Visual Studio

## Maintenance

### Updating SonarQube

```bash
# Pull latest image
docker-compose pull sonarqube

# Restart with new version
docker-compose up -d sonarqube
```

### Backup

Important directories to backup:
- `sonarqube_data` volume
- `postgres_data` volume

```bash
# Create backup
docker run --rm -v devgate_sonarqube_data:/data -v $(pwd):/backup alpine tar czf /backup/sonarqube-backup.tar.gz -C /data .
```