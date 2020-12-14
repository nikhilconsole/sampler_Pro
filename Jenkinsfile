pipeline{
    agent {label"linux-node"}
    options{
        buildDiscarder(logRotator(numToKeepStr:'3'))
    }
    stages{
        stage("download")
        {
            steps{
              git credentialsId: 'gitHUB_credentials', 
              url: 'https://github.com/CaramelIT-Harindra/reg-form.git'
              echo '#download stage finished'
            }
        }
        
        stage('Build'){
            steps{
                sh 'mvn package'
                echo '#Build stage finished'
            }
        }
        
        stage('Archieve Artifacts'){
            steps{
                archiveArtifacts artifacts: '**/*.war', followSymlinks: false
             
            }
        }
        stage('tomcat deploy'){
         steps{
    sh 'scp /home/ubuntu/workspace/Test-jobs/target/app.war ubuntu@172.31.19.219:/home/ubuntu/appserver/webapps/aaa.war'
        }
    }
    
    stage('code quality chcking'){
           steps{
        withSonarQubeEnv('sonarQube') {
          sh label: '', script: 'mvn sonar:sonar'
        }
        }
        }
        
            stage('Upload War file to Nexus'){
            steps{
                 nexusPublisher nexusInstanceId: 'nexus_server', 
                 nexusRepositoryId: 'Test', 
                 packages: [[$class: 'MavenPackage', 
                 mavenAssetList: [[classifier: '', extension: '',
                 filePath: '/home/ubuntu/workspace/Test-jobs/target/app.war']],
                 mavenCoordinate: [artifactId: 'app',
                 groupId: 'caramelit', 
                 packaging: 'war', 
                 version: '3.0']]]
               echo '#War file uploaded successfully'
            }
        }
    }
}
