node{
    stage('Clone'):{
        echo: ""
        git url https://github.com/hujingwinwin/k8s-deploy.git
        script:{
            built-tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD') 
        }
    }
    stage('Deploy'):{
        echo "Built"
        withCredentials([usernamePassword(credentialsId: 'dockerauth', passwordVariable: 'dockerauthUser', usernameVariable: 'dockerauthPass')]) {
            docker bulit -t hujingwinwin/k8s-jenkins-demo:${built-tag} .
            // 在jenkin中加入凭证
            docker loging -u${dockerauthUser} -p${dockerauthPass}
            docker push hujingwinwin/k8s-jenkins-demo:${built-tag}
        }
        
        def userInput = input(
            id: 'userInput',
            message: 'choice a env',
            parameters: [
                [
                    $class: 'ChoiceParameterDefinition:',
                    choice: 'Dev\nQA\nProd'
                    name: 'ENV'
                ]
            ]

        )
        
        echo "This is a env ${userInput}"

        sed -i 's/<BUILD_TAG>/${built-tag}/g' jenkins-k8s-demo.yaml

        kubectl apply -f jenkins-k8s-demo.yaml --record
    }
    stage('Test'):{
        echo "test"
    }
}