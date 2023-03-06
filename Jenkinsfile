pipeline
{
    agent
    {
        label 'docker-node'
    }
    environment
    {
        DOCKERHUB_CREDENTIALS=credentials('docker-hub')
    }
    stages
    {
        stage('Clone')
        {
            steps
            {
                git branch: 'main', url: 'http://192.168.56.102:3000/doadmin/bgapp'
            }
        }
        stage('Run')
        {
            steps
            {
                sh '''
                    cd /home/jenkins/workspace/BgApp
                    docker compose up -d
                '''
            }
        }
        stage('Test')
        {
            steps
            {
                script
                {
                    echo 'Test reachability'
                    sh 'sleep 3'
                    sh 'echo $(curl --write-out "%{http_code}" --silent --output /dev/null http://192.168.56.102:8080) | grep 200'
                    echo 'Test City Name'
                    sh 'sleep 3'
                    sh "curl --silent --data 'select * from cities where city_name = 'София'' http://192.168.56.102:8080 | grep София"
                }

            }
        }
        stage('CleanUp')
        {
            steps
            {
                sh '''
                    cd /home/jenkins/workspace/BgApp
                    sleep 5
                    docker compose down
                '''
            }
        }
        stage('Login to DockerHub')
        {
            steps
            {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Push')
        {
            steps
            {
                sh 'docker image tag bgapp-web vasilvelinov93/bgapp-web'
                sh 'docker image tag bgapp-db vasilvelinov93/bgapp-db'
                sh 'docker push vasilvelinov93/bgapp-web'
                sh 'docker push vasilvelinov93/bgapp-db'

            }
        }
        stage('Deploy')
        {
            steps
            {
                sh '''
                    cd /home/jenkins/workspace/BgApp
                    docker compose -f docker-compose2.yaml up -d
                '''
            }
        }
    }
}
