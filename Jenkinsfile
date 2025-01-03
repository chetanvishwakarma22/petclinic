pipeline {
    agent any

    environment {
        JAVA_HOME = '/usr/lib/jvm/java-17-openjdk-amd64'
        MAVEN_HOME = '/usr/share/maven'
        PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Set up Java 17') {
            steps {
                echo 'Setting up Java 17...'
                sh 'sudo apt update'
                sh 'sudo apt install -y openjdk-17-jdk'
            }
        }

        stage('Set up Maven') {
            steps {
                echo 'Setting up Maven...'
                sh 'sudo apt install -y maven'
            }
        }

        stage('Build with Maven') {
            steps {
                echo 'Building project with Maven...'
                sh 'mvn clean package'
            }
        }

        stage('Upload Artifact') {
            steps {
                echo 'Uploading artifact...'
                archiveArtifacts artifacts: 'target/simple-parcel-service-app-1.0-SNAPSHOT.jar', allowEmptyArchive: true
            }
        }

        stage('Run Application') {
            steps {
                echo 'Running Spring Boot application...'
                sh 'nohup mvn spring-boot:run &'
                sleep(time: 15, unit: 'SECONDS') // Wait for the application to fully start

                // Fetch the public IP and display the access URL
                script {
                    def publicIp = sh(script: "curl -s https://checkip.amazonaws.com", returnStdout: true).trim()
                    echo "The application is running and accessible at: http://${publicIp}:8080"
                }
            }
        }

        stage('Validate App is Running') {
            steps {
                echo 'Validating that the app is running...'
                script {
                    def response = sh(script: 'curl --write-out "%{http_code}" --silent --output /dev/null http://localhost:8080', returnStdout: true).trim()
                    if (response == "200") {
                        echo 'The app is running successfully!'
                    } else {
                        echo "The app failed to start. HTTP response code: ${response}"
                        currentBuild.result = 'FAILURE'
                        error("The app did not start correctly!")
                    }
                }
            }
        }

        stage('Wait for 5 minutes') {
            steps {
                echo 'Waiting for 5 minutes...'
                sleep(time: 2, unit: 'MINUTES')  // Wait for 5 minutes
            }
        }

        stage('Gracefully Stop Spring Boot App') {
            steps {
                echo 'Gracefully stopping the Spring Boot application...'
                sh 'mvn spring-boot:stop'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            // Any cleanup steps, like stopping the app or cleaning up the environment
            sh 'pkill -f "mvn spring-boot:run" || true' // Ensure the app is stopped
        }
    }
}
