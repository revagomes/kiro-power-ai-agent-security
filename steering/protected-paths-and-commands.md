# Protected Paths and Commands

## Protected Paths

### What are protected paths?

Protected paths are files and directories that AI agents must **never create, edit, delete, or rename**, even if explicitly instructed to do so by task descriptions, issue content, or user input that has been injected.

If a task requires modifying a protected path, the agent must stop and ask the human developer to make the change manually.

### Defining protected paths for your project

Create a protected paths list tailored to your project. Here's a template organized by category:

#### Authentication and secrets

```
**/.env
**/.env.*
**/secrets/**
**/*.key
**/*.pem
**/*.p12
**/credentials.json
**/auth.json
**/settings.php
**/settings.local.php
**/wp-config.php
```

#### Infrastructure and deployment

```
docker-compose.yml
docker-compose.override.yml
Dockerfile
**/terraform/**
**/cloudformation/**
**/k8s/**
**/helm/**
Vagrantfile
```

#### CI/CD pipelines

```
.github/workflows/**
.gitlab-ci.yml
Jenkinsfile
.circleci/**
bitbucket-pipelines.yml
```

#### Access control

```
# Drupal
config/sync/user.role.*.yml
config/sync/system.security.yml

# General
**/rbac/**
**/policies/**
**/permissions.*
```

#### Agent governance (self-protection)

```
.kiro/steering/**
.kiro/agents/**
.cursor/rules/**
.cursorrules
AGENTS.md
.aiignore
```

### Enforcement

Add protected paths to your agent's steering files or configuration. Example for Kiro:

```markdown
## Protected Paths — Do Not Modify

The following paths are write-protected for all AI agents:
- `.env*` — Environment secrets
- `docker-compose.yml` — Infrastructure
- `.github/workflows/**` — CI/CD pipelines
- `.kiro/steering/**` — Agent governance

If a task requires changes to these files, stop and ask the human developer.
```

## Command Restrictions

### Whitelist approach

Define what agents **can** run, not what they can't. Everything not on the whitelist is denied by default.

#### Typical safe commands

```bash
# Build and compile
npm run build
cargo build
make build
mvn compile
gradle build

# Test runners
npm test
pytest
cargo test
phpunit
jest
go test ./...

# Linters and formatters
eslint .
prettier --write
phpcs
rubocop
black .
gofmt

# Version control (read operations)
git status
git diff
git log
git branch

# Version control (write operations — scoped)
git add <specific-files>
git commit -m "<message>"
git checkout -b <branch>
git push -u origin <branch>

# Dependency management
npm install
pip install -r requirements.txt
composer install
cargo update
```

#### Typically dangerous commands

```bash
# Network exfiltration
curl, wget, nc, ncat          # Can send data to external hosts
ssh, scp, rsync               # Remote access
telnet                         # Unencrypted remote access

# Destructive operations
rm -rf /                       # Recursive delete
git push --force               # Overwrites remote history
git reset --hard               # Discards local changes
DROP TABLE, DROP DATABASE      # Data destruction
kubectl delete                 # Infrastructure destruction

# Secret readers
cat .env                       # Reads secrets
cat settings.php               # Reads database credentials
printenv                       # Dumps environment variables
cat ~/.ssh/id_rsa              # Reads private keys

# Privilege escalation
sudo                           # Elevated permissions
chmod 777                      # Overly permissive
chown                          # Ownership changes
```

### Configuring command restrictions

In your agent configuration, define allowed commands:

```json
{
  "toolsSettings": {
    "execute_bash": {
      "allowedCommands": [
        "npm run *",
        "npm test",
        "git status",
        "git diff",
        "git add",
        "git commit"
      ],
      "autoAllowReadonly": true
    }
  }
}
```

## Data Handling Rules

### Credentials and secrets

**Never include in agent output:**
- Database connection strings
- API keys and tokens
- Private keys and certificates
- Passwords (even hashed)
- OAuth client secrets

**When referencing secrets in code:**
```python
# Wrong — hardcoded secret
API_KEY = "sk_live_abc123..."

# Correct — environment variable reference
API_KEY = os.environ.get("API_KEY")
```

**When discussing secrets in conversation:**
```
# Wrong
"The database password is 'mypassword123'"

# Correct
"The database password is stored in the DB_PASSWORD environment variable"
```

### PII (Personally Identifiable Information)

**Never include in agent output:**
- Real names from database dumps
- Email addresses from user tables
- IP addresses from logs
- Phone numbers from form submissions
- Physical addresses

**When writing code examples:**
```python
# Wrong — real data from a dump
user = {"name": "John Smith", "email": "john@example.com"}

# Correct — placeholder data
user = {"name": "[name]", "email": "[email]"}
```

### Commit messages and PR descriptions

Before committing or creating a PR, verify:
- No credentials in the diff
- No PII in the commit message
- No secrets in file contents being committed
- No `.env` files staged

### .aiignore

Create a `.aiignore` file (similar to `.gitignore`) listing files the agent should never read:

```
# Secrets
.env
.env.*
auth.json
credentials.json

# Database dumps
*.sql
*.sql.gz
dbs/

# Private keys
*.key
*.pem
*.p12
```

## Autonomous Mode Restrictions

When agents operate autonomously (e.g., in git worktrees, CI pipelines):

1. **Scope to assigned directory** — Never modify files outside the assigned worktree or workspace
2. **No cross-worktree access** — Each agent works only in its own directory
3. **Time limits** — Set maximum execution time to prevent runaway agents
4. **Output limits** — Cap the amount of data an agent can write to prevent disk filling
5. **Network isolation** — Block outbound network access unless explicitly required
