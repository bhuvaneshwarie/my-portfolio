# Deploying this portfolio with Jenkins

This document explains two common ways to deploy a static site (index.html) via Jenkins:
- GitHub Pages (recommended for static HTML portfolios)
- SSH deploy to a web server (e.g., /var/www/html)

-- Quick overview
1. Add a Jenkinsfile to your repo (already added). It contains a pipeline with a `DEPLOY_TARGET` parameter.
2. Configure credentials in Jenkins: `github-token` (Secret text) and/or `server-ssh` (SSH username + private key).
3. Create a Pipeline job in Jenkins which uses this repository.
4. Run the job and select `github-pages` or `ssh` to deploy.

-- GitHub Pages setup (recommended)
1. In GitHub, create the `portfolio` repository if not created.
2. In Jenkins:
   - Install plugins if necessary: Git, Pipeline, Credentials, GitHub Integration.
   - Add a credential of type **Secret text** with ID: `github-token`.
     - The secret value is a Personal Access Token with `repo` scope.
3. Create a Pipeline job:
   - Pipeline script from SCM -> Git repository URL -> branch main
   - Save
4. Run the build and choose `DEPLOY_TARGET = github-pages`.
5. The job will create an orphan `gh-pages` branch and push the build (forced).
6. Enable GitHub Pages in repository settings: Settings -> Pages -> Source -> `gh-pages` branch.

-- SSH / Server deploy
1. Create an SSH credential in Jenkins (SSH Username with private key) and give it ID `server-ssh`.
2. Ensure the Jenkins agent has `ssh` and `scp` commands available.
3. Update the `Jenkinsfile` or job to use correct remote `SSH_USER` and `host`.
4. Run the job with `DEPLOY_TARGET = ssh` to copy site files to `/var/www/html`.

-- Security notes
- Never hard-code tokens in your repository. Use Jenkins credentials and `withCredentials`.
- Limit token scope to `repo` for GitHub Pages deployment.

-- Trouble-shooting
- 403 on push: check token validity and `repo` scope.
- Permission denied on SSH: add your public key to the server's `~/.ssh/authorized_keys` for the user used by `server-ssh`.
- If your site is not visible, make sure GitHub Pages settings are configured and your `gh-pages` branch is served.

-- Example: Running locally to simulate build
If you want to test without Jenkins, create a local branch and push it manually:

```powershell
git checkout --orphan gh-pages
git rm -rf .
cp -r ../portfolio/* .
git add -A
git commit -m "Deploy site"; git push origin gh-pages --force
```

If you prefer a hosted provider like Netlify or Vercel, their GitHub auto-deploy integrations work easily from this repository.
