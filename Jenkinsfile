pipeline {
  agent any
  parameters {
    choice(name: 'DEPLOY_TARGET', choices: ['github-pages', 'ssh'], description: 'Choose deployment target')
  }

  environment {
    GITHUB_REPO = 'https://github.com/bhuvaneshwarie/portfolio.git'
    SITE_DIR = 'site_out'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build (optional)') {
      steps {
        echo 'No build step needed for a static site (index.html).'
      }
    }

    stage('Deploy') {
      steps {
        script {
          if (params.DEPLOY_TARGET == 'github-pages') {
            echo 'Deploying to GitHub Pages...'
            withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
              sh '''
                set -e
                rm -rf ${SITE_DIR} || true
                mkdir -p ${SITE_DIR}
                # Copy only static site files (adjust if you have CSS/JS folders)
                cp -r $(ls -A | grep -v .git) ${SITE_DIR}/ || true
                cd ${SITE_DIR}
                git init
                git config user.name "Jenkins"
                git config user.email "jenkins@localhost"
                git add -A
                git commit -m "Deploy from Jenkins: ${BUILD_NUMBER}" || true
                git push -f https://${GITHUB_TOKEN}@github.com/bhuvaneshwarie/portfolio.git master:gh-pages
              '''
            }
          } else {
            echo 'Deploying to remote server via SSH...'
            // Example: Use Publish over SSH plugin or sshagent credentials (server-ssh)
            sshagent(['server-ssh']) {
              sh 'scp -r * ${SSH_USER}@your.server.example:/var/www/html'
            }
          }
        }
      }
    }
  }

  post {
    success {
      echo 'Deployment finished successfully.'
    }
    failure {
      echo 'Deployment failed.'
    }
  }
}
