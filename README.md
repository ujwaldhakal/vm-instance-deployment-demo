# How to automatically Deploy Code to VM Instance using Github Action?
If you are still deploying code to Server by manually logging into server & running the commands then you need to automate and save sometime, because life is too short to deploy manually.

In this tutorial you will learn how to write a script that will deploy code to the Server using [Github Action](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions) on every code push to Github

### Prerequisites

* Basic concepts  Github Actions Workflows
* You have hosted application i.e any frontend / backend application on any virtual server (Digital Ocean, Vm instance, ec2 instance etc)

### What is Github Action?
[Github Action](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions) is a service provided by Github where one can listen to the various Github Events like code pushed to some branch, tag creation etc and react to them accordingly with some scripts which we will write.

### What is Vm Instance?
A [Vm Instance](https://cloud.google.com/compute/?utm_source=google&utm_medium=cpc&utm_campaign=japac-AU-all-en-dr-bkws-all-all-trial-e-dr-1009882&utm_content=text-ad-none-none-DEV_c-CRE_505020325174-ADGP_Hybrid+%7C+BKWS+-+EXA+%7C+Txt+~+Compute+~+Compute+Engine_VM-instances-KWID_43700062034862769-kwd-277810790368&userloc_9070016-network_g&utm_term=KW_google%20vm%20instance&gclsrc=ds&gclid=CPLrvKvrzfACFUNyjgodZg8OfA&gclsrc=ds) is a virtual machine (VM) hosted on Google's infrastructure where our codes & webserver will be hosted into.

### Solution Approach
In this tutorial we will use [Nuxt Js](https://nuxtjs.org/) application. So to deploy the Nuxt Js application lets create a clear manual steps and try to automate it.

* Log into the Server via ssh
* Go to the right folder and pull git code using `git pull origin master`
* Run `yarn install` if there are any new dependencies
* Run `yarn build` to build the file
* Run `yarn start`

Now lets create a script which will trigger this all these commands whenever we push any code to `master` branch on Github

### Write Github Action Yaml and Bash script 
We will create two files(Code Repository & Server) which will get triggered on code push.
##### Create Github Action Deployment file `deploy.yml`
Lets create `.github/workflows` folder inside your application root directory and create a file `deploy.yml` with following codes

```
name: deploy
on:
    push:
      tags:
        - 'master'
jobs:

  build:
    name: Build
    runs-on: ubuntu-latest
    steps:
    - name: executing remote ssh commands using password
      uses: appleboy/ssh-action@master
      with:
        host: ${{ secrets.HOST }}
        username: ${{ secrets.USERNAME }}
        password: ${{ secrets.PASSWORD }}
        port: ${{ secrets.PORT }}
        script: sh deploy-app.sh
```
In this script we gave our deployment a name `deploy` which will be triggered whenever we push any codes to master i.e ( directly code push to master or merging of any branch ). Then we have defined a `build` step where we have used package from `appleboy/ssh-action` which will help us to run `script:sh deploy-app.sh` script with environment variables. 

*Note: The  equivalent command  of this script is `shh username@host sh deploy-app.sh`*

The variable named `secrets` are the key & value store which is provided by [Github Secrets](https://docs.github.com/en/actions/reference/encrypted-secrets). Add `HOSTNAME,USERNAME,PASSWORD,PORT` these variable with value in Github Secrets where you can find under `settings > Secrets > New repository secret`
![Github Secrets](https://nimbus-screenshots.s3.amazonaws.com/s/c57a58d4d4c87a7c866728521ff53c4d.png)

##### Create Bash file `deploy-app.sh` 
After Github Action logs into the server from ssh it needs to trigger some manual commands to pull new version from Github and run necessary steps. Create `deploy-app.sh` inside your server where your application is hosted. And inside that file write following manual steps.

```
git pull origin master && 
yarn install &&
yarn build && ssh
yarn start
```

Now whenever you push any code to `master` Github Action will use our `deploy.yaml` to trigger script `sh deploy-app.sh` and we can see our new version of master branch. 

### Conclusion

So in this tutorial your learned how to make a simple ssh deployment via Github Action.Now you can use same techniques to automate any application with Github Actions via ssh. 
Let me know your thoughts !

Github Repo Source : [Deployment Demo](https://github.com/ujwaldhakal/vm-instance-deployment-demo)