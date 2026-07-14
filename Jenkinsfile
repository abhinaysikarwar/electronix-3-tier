pipeline {
    agent { label 'electronix' }

    environment{
        s3_BUCKET='electronix-agent-abhinay'
        CLOUDFRONT_ID='E1BHWCIPBY5YDV'
        AWS_REGION='US-EAST-1'
    }

    stages {
        stage("Frontend Deployment") {
            when{
                changeset "fronted/**"
            }

            stages{
                stage('Install Dependencies'){
                    steps{
                        dir('frontend'){
                            sh'''
                            sh "npm install"
                        }
                    }
                }

                stage("Run Tests"){
                    steps{
                        dir(frontend){
                            sh 'npm test -- --watchAll=false || echo "No Test configured.."'
                        }
                    }
                } 

                stage("Build"){
                    steps{
                        dir(frontend){
                            sh 'npm run build'
                        }
                    }
                } 

                stage('Deploy s3'){
                    steps{
                        dir('frontend'){
                            sh '''
                            aws s3 sync dist/ s3://${s3-BUCKET} --delete --regi0on ${AWS_REGION}
                        }
                    }
                } 

                stage('Invalidation Cloudfront Cache'){
                    steps{
                        sh '''
                        aws cloudfront create-invalidation --distrubution-id ${CLOUDFRONT_ID} --paths "/*"
                    }
                }              
            }
        }
    }

    Post{
        success{
            'Frontend Deoloyment Successful✅'
        }

        Faliuer{
            'frontend Deployment Failed❌'
        }
    }
}