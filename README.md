GitHub Actions Instead of Travis CI
If you do not wish to use Travis CI, or, you have run out of credits, we have provided code and guidance to use GitHub Actions instead. GitHub Actions is a free CI/CD automation tool built directly into GitHub repositories. It eliminates the need for external services.

How GitHub Actions Works
GitHub Actions uses YAML files to define workflows, which are automated processes that run one or more jobs. These jobs are sets of steps that execute on the same runner, which is a virtual machine. Steps within a job can run commands, setup tasks, or run an action. GitHub Actions revolves around four main concepts: triggers (when to run), jobs (what to do), steps (how to do it), and actions (reusable units of code).

Folder Structure
project-root/
├── .github/
│   └── workflows/
│       └── main.yml
└── (rest of your project files)


The .github/workflows directory in your repository root is where you place your workflow files.



Replacement Code
For every lecture that we write Travis configuration code, we have provided replacement code that uses GitHub Actions instead. This is available as a separate zip file that has the words gh-actions within it.

example:

Lecture 88: A Touch More Travis Setup

Refer to the zip file:

88-touch-more-gh-actions.zip



You may also refer to the completed and finished example here (scroll past the lecture video to see the featured question):


To replace Travis with Github Actions you can do the following:

1. Delete your .travis.yml file from the local project.

2. Navigate to your Github repository.

3. Click Settings

4. Click Secrets

5. Click Actions

6. Click New repository secret

7. Create key/value pair secrets for AWS_ACCESS_KEY, AWS_SECRET_KEY, DOCKER_USERNAME, DOCKER_PASSWORD.

Important - You do not need to create an environment or environment secrets. Doing so will cause a failure of the action without making additional changes to the workflow file.

All that is required is simply creating the repository secrets as shown below:
https://docs.github.com/es/codespaces/managing-your-codespaces/managing-your-account-specific-secrets-for-github-codespaces

https://img-c.udemycdn.com/redactor/raw/q_and_a_edit/2022-10-18_15-42-51-274103e4e22764ebfbeb809f26fdea24.png

8. In your local development environment, create a .github directory at the root of your project

9. Create a workflows directory inside the new .github directory

10. In the workflows directory create a deploy.yaml file which should contain the following code (name does not matter):

* Remember to change your application_name, environment_name, existing_bucket_name, and region to the values used by your AWS Elastic Beanstalk environment.

* Remember to check your default branch to see if it is main or master and update the branches field accordingly.
https://docs.github.com/es/actions/writing-workflows/about-workflows





name: Deploy Frontend
on:
  push:
    branches:
      - main # check your repo, your default branch might be master!
 
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: docker login -u ${{ secrets.DOCKER_USERNAME }} -p ${{ secrets.DOCKER_PASSWORD }}
      - run: docker build -t cygnetops/react-test -f Dockerfile.dev .
      - run: docker run -e CI=true cygnetops/react-test npm test
 
      - name: Generate deployment package
        run: zip -r deploy.zip . -x '*.git*'
 
      - name: Deploy to EB
        uses: einaregilsson/beanstalk-deploy@v21
        with:
          aws_access_key: ${{ secrets.AWS_ACCESS_KEY }}
          aws_secret_key: ${{ secrets.AWS_SECRET_KEY }}
          application_name: docker-gh
          environment_name: Dockergh-env
          existing_bucket_name: elasticbeanstalk-us-east-1-923445559289
          region: us-east-1
          version_label: ${{ github.sha }}
          deployment_package: deploy.zip


11. Run the typical git add, commit and push commands

12. Click Actions in the Github repository dashboard to view each step of the workflow.

Note - This code is using a well-supported marketplace action, more info can be found here:

https://github.com/einaregilsson/beanstalk-deploy