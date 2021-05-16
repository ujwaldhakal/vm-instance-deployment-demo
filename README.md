# How to automatically Deploy Code to VM Instance using Github Action?
After making an application the next overhead is making a fluent deployment process where code can be easily be deployed to the serven. But Github has made it very using introducing the Github[Github Action](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions)

In this tutorial you will learn how to write a script that will deploy our desired branch to the server using [Github Action](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions)

### Prerequisites
* Working server which can be any VM Instance, Digital Ocean Droplet, Ec2 instance etc. I will be using VM Instance
* Basic concepts of writing & trigger Github Actions 
* You have hosted application i.e any frontend / backend application on any virtual server (Digital Ocean, Vm instance, ec2 instance etc)




### What is Github Action?
[Github Action](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions) is a service provided by Github where one can listen to the various Github Events like code pushed some branch, tag creation etc and react to them accordingly with some scripts which we will write.

### What is Vm Instance?
A [Vm Instance](https://cloud.google.com/compute/?utm_source=google&utm_medium=cpc&utm_campaign=japac-AU-all-en-dr-bkws-all-all-trial-e-dr-1009882&utm_content=text-ad-none-none-DEV_c-CRE_505020325174-ADGP_Hybrid+%7C+BKWS+-+EXA+%7C+Txt+~+Compute+~+Compute+Engine_VM-instances-KWID_43700062034862769-kwd-277810790368&userloc_9070016-network_g&utm_term=KW_google%20vm%20instance&gclsrc=ds&gclid=CPLrvKvrzfACFUNyjgodZg8OfA&gclsrc=ds) is a virtual machine (VM) hosted on Google's infrastructure where our codes & webserver will be hosted into.

### Solution Approach
In this app we will demo use Nuxt Js. So to deploy the Nuxt Js application lets create a clear manual steps and try to automate it using tools. So here are the steps involved -:

* Log into the server via ssh
* Go to the right folder and pull git code using `git pull origin master`
* Run `yarn install` if there are any new dependencies
* Run `yarn build` to build the file
* Run `yarn start`

If you could manage to run these  automatically whenever some code has been deployed to master.

### Write Github Action Yaml and Bash script 

##### 1) Create Github Action Deployment file `deploy.yml`
Lets create `.github/workflows` folder from your application root directory and create a file `deploy.yml` with following codes

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
In this script we gave a our deployment name `deploy` which will be triggered whenever we push any codes to master i.e directly code push to master & merging of any branch. Then we have defined a `build` step where we have used package from `appleboy/ssh-action` which will help us to run `script:sh deploy-app.sh` script with environment variables. The final manual equavalent of this script will look this `shh username@host sh deploy-app.sh`

The variable named `secrets` are the key & value store which is provided by (Github Secrets)[https://docs.github.com/en/actions/reference/encrypted-secrets]. You need to `HOSTNAME,USERNAME,PASSWORD,PORT` these variable with value in Github Secrets where you can find under `settings > Secrets > New repository secret`
![alt text](https://nimb.ws/cuKmVk)

##### 2) Create Bash file `deploy-app.sh` 
Now the final piece of the puzzle is to create `deploy-app.sh` inside your server where your application is hosted. And inside that file create our manual steps which we had discussed earlier to deploy.
`deploy-app.sh`

```
git pull origin master && 
yarn install &&
yarn build && 
yarn start
```

Now whenever you push any code to `maste`r Github Action will use our `deploy.yaml` to trigger script `sh deploy-app.sh` and we can see our new version of master branch. 

### Conclusion

So in this tutorial your learned how to make a simple ssh deployment via Github Action.Now You can use same techniques automate any application with Github Actions via SSH. 
Let me know your thoughts !

Github Repo Source : [Deployment Demo](https://github.com/ujwaldhakal/vm-instance-deployment-demo)