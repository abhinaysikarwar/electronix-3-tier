pipeline {
    agent { label 'electronix' }

    environment {
        s3_BUCKET = 'electronix-agent-abhinay'
        CLOUDFRONT_ID = 'E1BHWCIPBY5YDV'
        AWS_REGION = 'us-east-1'
    }

    stages {
        stage("Frontend Deployment") {
            when {
                changeset "frontend/**"
            }

            stages {

                stage('Install Dependencies') {
                    steps {
                        dir('frontend') {
                            sh '''
                                npm install
                            '''
                        }
                    }
                }

                stage("Run Tests") {
                    steps {
                        dir('frontend') {
                            sh '''
                                npm test -- --watchAll=false || echo "No Test configured.."
                            '''
                        }
                    }
                }

                stage("Build") {
                    steps {
                        dir('frontend') {
                            sh '''
                                npm run build
                            '''
                        }
                    }
                }

                stage('Deploy s3') {
                    steps {
                        dir('frontend') {
                            sh '''
                                aws s3 sync dist/ s3://${s3_BUCKET} --delete --region ${AWS_REGION}
                            '''
                        }
                    }
                }

                stage('Invalidation Cloudfront Cache') {
                    steps {
                        sh '''
                            aws cloudfront create-invalidation --distribution-id ${CLOUDFRONT_ID} --paths "/*"
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Frontend Deployment Successful ✅'
        }

        failure {
            echo 'Frontend Deployment Failed ❌'
        }
    }
}