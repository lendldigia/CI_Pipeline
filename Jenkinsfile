#!/usr/bin/env groovy

node {
    stage('Test')
    {
        echo 'Running Jenkins file'
    }
    
    stage('Checkout'){
    checkout scm
    }

    stage('Create API'){
    sh ''' cid=$(curl -k -X POST -H "Authorization: Basic YWRtaW46YWRtaW4=" -H "Content-Type: application/json" -d @payload.json https://localhost:9443/client-registration/v0.11/register | jq -r '.clientId')
           cs=$(curl -k -X POST -H "Authorization: Basic YWRtaW46YWRtaW4=" -H "Content-Type: application/json" -d @payload.json https://localhost:9443/client-registration/v0.11/register | jq -r '.clientSecret')

echo ${cid}
echo ${cs}

encodeClient="$(echo -n $cid:$cs | base64)"

echo ${encodeClient}

token=$(curl -k -d "grant_type=password&username=admin&password=admin&scope=apim:api_create" -H "Authorization: Basic $encodeClient" https://localhost:8243/token | jq -r '.access_token')

echo ${token}

echo "Final response is written to createOutput.json file"

curl -k -H "Authorization: Bearer $token" -H "Content-Type: application/json" -X POST -d @createDEMO.json https://localhost:9443/api/am/publisher/v0.11/apis >> createOutput.json
#echo ',' >>createOutput.json

databasename=`jq '{code: .code , msg: .message}' createOutput.json`

echo ${databasename}'''
    }
    
    stage('Postbuild')
    {
    sh ''' 
    git url: "git@github.com:lendldigia/CI_Pipeline.git",
    credentialsId: 'ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAgEAxfXJcBrQO8Oc820n5j61gZWDXUuDkzjYRmjWJhZJG03t+c/I6LglMKZDIf6h9DzuLCedjixFZZglmUk8T2WsYQYrbJTwxI+WzbEi8DI/Y6pXhEOgoahj/pkR5ZYFaDvyEFPhOw9qLwW/HcnVVIXfeGZoGlZpjN41NmFXKovFtqZ5WRQnoJyTB+bmY3GEw1HW/CSV/TyDQ4KiTbj/P58IJ5rFldyYaWqw8N57/k+ADK+6roNzeMIgstchA7GInr7zEWR4Iej7TPVCRmBnwRK9paNrzOHemst8kGPjbIVsdJpqcdMumC5+Q/3mKjp3B0fr5KPrBEX5zZvmnRm0Eh4Dw6PPjG5yOlIZkGGY3JMz9taCNa7cN8sy1UACmgpYFaxui16nsDY4kCW6IPfn8bNhKo4mhHfNC0rgLfYLtYQSo7RUCauSRJ7JZG57drb7/7Mz5KlpjC6RnA2FjPC9coYNB2Z+Weje4QrsVgYiMfmWR1UXPGmdoJiEJLtDQ5bBMcW51zF7FCSmmTTaQbyemUrGdmPzNH6CjtKL9ffRyO9n24DHKv8Rn/OuIF/nVvHaAUlXr+KiQ7qmeDzs37EAxt7TwYuzXE8p1dR9Fw+xNglU2UWfOwPRy1IBnQ8h/2AFUFeYUhXi42/VWh9tFhoaV3IItw8YiX6gPTJWlySHKEZ3cO8= lendl.gomes@digia.com',
    branch: master
    
    git config --global user.email "lendl.gomes@digia.com"
    git config --global user.name "lendldigia"
    git fetch
        git checkout "master"
git pull
    git add createOutput.json
    git commit -am 'Testing if file added'
    git push origin master
    '''
    }
  }
