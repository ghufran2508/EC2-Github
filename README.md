# EC2-Github
A workflow to connect github action with ec2 instance using ssh key and perfrom commands

Add secrets in github secret actions...

pre requisite:
- setup your github account (ssh-key) on ec2.
- create folders on ec2 following same name as branches. (can easily navigate using github.ref_name.
- intall pm2 (commands e.g: list, start, restart, remove)
- install language suuport / compiler

```
name: Update EC2 Instance

on:
  push:
    branches:
      - dev
      - qa
      - staging
      - production

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: 18

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}
          PRIVATE_KEY: ${{ secrets.EC2_INSTANCE_KEY }}
      - name: Connect and Update code on ec2
        run: |
            echo "${{ secrets.EC2_INSTANCE_KEY }}" > private_key && chmod 600 private_key
            ssh -o StrictHostKeyChecking=no -i private_key ${{secrets.EC2_IP}} '
            cd ${{github.ref_name}} && git pull origin ${{github.ref_name}} && npm i && pm2 restart ${{github.ref_name}} && exit
            '
```
