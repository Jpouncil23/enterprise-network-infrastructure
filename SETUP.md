# 📤 GitHub Setup Instructions

This guide will help you upload your Enterprise Network Infrastructure project to GitHub.

## Prerequisites

- GitHub account (create one at https://github.com if you don't have one)
- Git installed on your computer
- Command line access (Terminal on Mac/Linux, Git Bash or PowerShell on Windows)

## Step 1: Install Git (if not already installed)

### Windows
Download and install from: https://git-scm.com/download/win

### Mac
```bash
# Using Homebrew
brew install git

# Or download from
https://git-scm.com/download/mac
```

### Linux
```bash
# Ubuntu/Debian
sudo apt-get install git

# Fedora/RHEL
sudo dnf install git
```

## Step 2: Configure Git (First Time Setup)

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

## Step 3: Create GitHub Repository

1. Go to https://github.com
2. Log in to your account
3. Click the **"+"** icon in top right
4. Select **"New repository"**
5. Repository settings:
   - **Repository name**: `enterprise-network-infrastructure`
   - **Description**: `Enterprise network infrastructure build with multi-zone architecture and defense-in-depth security`
   - **Visibility**: Choose Public or Private
   - **DO NOT** initialize with README (we already have one)
6. Click **"Create repository"**

## Step 4: Upload Your Project

### Option A: Using Command Line (Recommended)

1. **Navigate to your project folder** (where you downloaded these files):
   ```bash
   cd /path/to/github-project
   ```

2. **Initialize Git repository**:
   ```bash
   git init
   ```

3. **Add all files**:
   ```bash
   git add .
   ```

4. **Create initial commit**:
   ```bash
   git commit -m "Initial commit: Enterprise Network Infrastructure Project"
   ```

5. **Add GitHub remote** (replace YOUR-USERNAME with your GitHub username):
   ```bash
   git remote add origin https://github.com/YOUR-USERNAME/enterprise-network-infrastructure.git
   ```

6. **Push to GitHub**:
   ```bash
   git branch -M main
   git push -u origin main
   ```

### Option B: Using GitHub Desktop (Easier for Beginners)

1. Download GitHub Desktop: https://desktop.github.com/
2. Install and sign in to GitHub
3. Click **"File"** → **"Add Local Repository"**
4. Select your `github-project` folder
5. Click **"Publish repository"**
6. Choose repository name and visibility
7. Click **"Publish repository"**

### Option C: Drag and Drop via GitHub Web Interface

1. Go to your newly created repository on GitHub
2. Click **"uploading an existing file"**
3. Drag and drop all folders and files
4. Add commit message: "Initial commit: Enterprise Network Infrastructure Project"
5. Click **"Commit changes"**

**Note:** This method is simplest but won't preserve the commit history structure.

## Step 5: Verify Upload

1. Go to your repository URL: `https://github.com/YOUR-USERNAME/enterprise-network-infrastructure`
2. You should see:
   - ✅ README.md displaying on the main page
   - ✅ `docs/` folder with all documentation
   - ✅ `diagrams/` folder with network topology
   - ✅ `configs/` folder (for future configuration files)

## Step 6: Customize Your Repository

### Add Topics (Tags)
1. Click the gear icon ⚙️ next to "About"
2. Add topics: `networking`, `enterprise`, `vyos`, `opnsense`, `firewall`, `infrastructure`, `cybersecurity`

### Add Description
1. Click the gear icon ⚙️ next to "About"
2. Add description: "Enterprise network infrastructure with multi-zone architecture, VyOS routing, OPNsense firewall, and defense-in-depth security"

### Enable GitHub Pages (Optional - for HTML diagram)
1. Go to **Settings** → **Pages**
2. Source: **Deploy from a branch**
3. Branch: **main** / folder: **/(root)**
4. Click **Save**
5. Your network diagram will be available at: `https://YOUR-USERNAME.github.io/enterprise-network-infrastructure/diagrams/network_topology_diagram.html`

## Ongoing Usage

### Making Updates

When you make changes to your documentation:

```bash
# 1. Check what changed
git status

# 2. Add changed files
git add .

# 3. Commit with descriptive message
git commit -m "Updated firewall configuration documentation"

# 4. Push to GitHub
git push
```

### Creating Branches (For Major Changes)

```bash
# Create and switch to new branch
git checkout -b feature/add-monitoring-docs

# Make your changes, then commit
git add .
git commit -m "Added monitoring and analytics documentation"

# Push branch to GitHub
git push -u origin feature/add-monitoring-docs

# Then create Pull Request on GitHub web interface
```

## Project Structure

```
enterprise-network-infrastructure/
├── README.md                          # Main project documentation
├── .gitignore                         # Git ignore rules
├── SETUP.md                           # This file
├── docs/                              # Documentation folder
│   ├── Enterprise_Network_Build_Report.docx
│   ├── network_topology.md
│   ├── Enterprise_Network_Requirements.docx
│   └── Enterprise_Network_Requirements_-_Task_Assignment.docx
├── diagrams/                          # Network diagrams
│   └── network_topology_diagram.html
└── configs/                           # Configuration files (future)
    └── (router and firewall configs can go here)
```

## Tips for a Professional GitHub Repository

### 1. Write Good Commit Messages
- ✅ "Added firewall rule documentation for Screened Subnet"
- ❌ "Updated files"

### 2. Update README Regularly
Keep the README.md up to date as you add new features or configurations

### 3. Use Issues for Tracking
Create GitHub Issues for:
- Future enhancements
- Known issues
- Configuration improvements

### 4. Add a License (Optional)
If you want to specify how others can use your documentation:
1. Create `LICENSE` file
2. Choose a license at https://choosealicense.com/
3. Common choices: MIT, Apache 2.0, or CC BY 4.0 for documentation

### 5. Enable Discussions (Optional)
Go to **Settings** → **Features** → Enable **Discussions** for Q&A

## Troubleshooting

### Authentication Issues
If you get authentication errors:

**Option 1: Use Personal Access Token**
1. Go to GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Generate new token with `repo` scope
3. Use token as password when pushing

**Option 2: Use SSH**
1. Generate SSH key: `ssh-keygen -t ed25519 -C "your_email@example.com"`
2. Add to GitHub: Settings → SSH and GPG keys → New SSH key
3. Use SSH URL: `git@github.com:YOUR-USERNAME/enterprise-network-infrastructure.git`

### Large File Issues
If Word documents are too large (>100MB):
- Consider converting to PDF
- Use Git LFS: https://git-lfs.github.com/

### Merge Conflicts
If you edit on GitHub web interface and locally:
```bash
git pull
# Resolve conflicts in your editor
git add .
git commit -m "Resolved merge conflicts"
git push
```

## Need Help?

- GitHub Docs: https://docs.github.com/
- Git Cheat Sheet: https://education.github.com/git-cheat-sheet-education.pdf
- GitHub Learning Lab: https://lab.github.com/

## Next Steps

After uploading to GitHub:
1. ✅ Share your repository URL with instructors/team
2. ✅ Add collaborators (Settings → Collaborators)
3. ✅ Star your own repository (makes it easier to find)
4. ✅ Keep documentation updated as you complete more tasks
5. ✅ Use GitHub Issues to track remaining work

---

**Your project is now version controlled and publicly/privately accessible!** 🎉
