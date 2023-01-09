pipeline{
    agent{
        label "slave"
    }
    stages{
        stage("Build Started"){
            steps{
            sh '''
                    curl -s -X POST https://api.telegram.org/bot5988052752:AAEsBVdnpFaainVkCE_ns5IQOGdsvy9tKTc/sendMessage -d chat_id=-1001804879191 -d parse_mode="HTML" -d text="Build Started ${JOB_NAME} ${BUILD_ID}"
				'''
            }
        }
        stage("Clearning workspace"){
            steps{
            cleanWs()
            }
        }
        stage("Git pull"){
            steps{
            checkout(
                [
                    $class: 'GitSCM', branches: [
                        [name: '*/staging']
                        ], 
                    extensions: [], 
                    userRemoteConfigs: [
                        [url: 'https://github.com/arvindgkmit/pm']
                        ]
                ])
            }
        }
        stage("Docker Notification Started"){
            steps{
                sh '''
                    curl -s -X POST https://api.telegram.org/bot5988052752:AAEsBVdnpFaainVkCE_ns5IQOGdsvy9tKTc/sendMessage -d chat_id=-1001804879191 -d parse_mode="HTML" -d text="Image Build Started ${JOB_NAME} ${BUILD_ID}"
				'''
            }
        }
        stage("Docker Push"){
            steps{
                script{
                    def image1 = docker.build(
                        "vaibhavrajnathchauhan/project-management:${BUILD_ID}",
                         "--file Dockerfile ."
                         )
	                image1.push()
                }
            }
        }
        stage("Docker Notification Completed"){
            steps{
                sh '''
                    curl -s -X POST https://api.telegram.org/bot5988052752:AAEsBVdnpFaainVkCE_ns5IQOGdsvy9tKTc/sendMessage -d chat_id=-1001804879191 -d parse_mode="HTML" -d text="Image Build Completed ${JOB_NAME} ${BUILD_ID}"
				'''
            }
        }
        stage("Removing Image"){
            steps{
                sh "docker rmi -f vaibhavrajnathchauhan/expense-sharing:${BUILD_ID}"
            }
        }
        stage("Docker Notification"){
            steps{
                sh '''
                    curl -s -X POST https://api.telegram.org/bot5988052752:AAEsBVdnpFaainVkCE_ns5IQOGdsvy9tKTc/sendMessage -d chat_id=-1001804879191 -d parse_mode="HTML" -d text="Ansible Build Started ${JOB_NAME} ${BUILD_ID}"
				'''
            }
        }
        stage("Ansible playbook"){
            steps{
            ansiblePlaybook credentialsId: '125c65a4-99ea-48f5-8fd5-50bd6c9ac735', 
                installation: 'Ansible', 
                inventory: '/home/ec2-user/arvind/dev.inv' ,
                playbook: '/home/ec2-user/arvind/main.yml' ,
                extraVars   : [
                BUILD_ID: "${ BUILD_ID }"
                ]
            }
        }
    }
    post{
        success{
                sh '''
                    curl -s -X POST https://api.telegram.org/bot5988052752:AAEsBVdnpFaainVkCE_ns5IQOGdsvy9tKTc/sendMessage -d chat_id=-1001804879191 -d parse_mode="HTML" -d text="Build Completed ${JOB_NAME} ${BUILD_ID}"
				'''
        }
        failure{
            sh'''
                curl -s -X POST https://api.telegram.org/bot5988052752:AAEsBVdnpFaainVkCE_ns5IQOGdsvy9tKTc/sendMessage -d chat_id=-1001804879191 -d parse_mode="HTML" -d text="Build Failed ${JOB_NAME} ${BUILD_ID}"

            '''

        }
    }
}





