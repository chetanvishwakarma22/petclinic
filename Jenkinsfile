@Library('my-shared-library@main') _

pipeline {
    agent { label 'slave' }

    environment {
        JAVA_HOME = '/usr/lib/jvm/java-17-openjdk-amd64'
        MAVEN_HOME = '/usr/share/maven'
        PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkoutCode()
            }
        }

        stage('Set up Java 17') {
            steps {
                setupJava()
            }
        }

        stage('Set up Maven') {
            steps {
                setupMaven()
            }
        }

        stage('Build with Maven') {
            steps {
                buildProject()
            }
        }

        stage('Upload Artifact') {
            steps {
                echo 'Uploading artifact...'
                archiveArtifacts artifacts: 'target/petclinic-0.0.1-SNAPSHOT.jar', allowEmptyArchive: true
            }
        }

        stage('Run Application') {
            steps {
                runApplication()
            }
        }

        stage('Validate App is Running') {
            steps {
                validateApp()
            }
        }

        stage('Gracefully Stop Spring Boot App') {
            steps {
                stopApplication()
            }
        }
    }

    post {
        always {
            cleanup()
        }
    }
}
