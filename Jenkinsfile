pipeline {
    agent any


    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'master', url: 'https://github.com/clamprou/devops-docker.git'

                
            }
        }
        
        stage('Test') {
            steps {
                sh '''
                    python3 -m venv myvenv
                    source myvenv/bin/activate
                    pip install -r requirements.txt
                    cd myproject
                    cp myproject/.env.example myproject/.env
                    ./manage.py test'''
            }
        }
        stage('install ansible prerequisites') {
            steps {
                sh '''
                    ansible-galaxy install geerlingguy.postgresql
                '''

                sh '''
                    mkdir -p ~/workspace/ansible-pipeline/files/certs
                    cd ~/workspace/ansible-pipeline/files/certs
                    openssl req -x509 -newkey rsa:4096 -keyout server.key -out server.crt -days 365 --nodes -subj '/C=GR/O=myorganization/OU=it/CN=myorg.com'
                '''
            }
        }
        // stage('Prepare DB') {            
        //     steps {
        //         sshagent (credentials: ['ssh-app01-1']) {
        //             sh '''
        //                 pwd
        //                 echo $WORKSPACE
        //                 ansible-playbook -i ~/workspace/ansible-pipeline/hosts.yml -l database ~/workspace/ansible-pipeline/playbooks/postgres.yml
        //                 '''
        //     }
        //     }
        // }
        stage('install docker to vm 1') {
            steps{
                sshagent (credentials: ['ssh-app01-1']) {
                    sh '''
                        ansible-playbook -i ~/workspace/ansible-pipeline/hosts.yml -l webserver ~/workspace/ansible-pipeline/playbooks/docker-install.yml
                    '''
                }

            }

        }
        
        stage('deploym to vm 1') {
            steps{
                sshagent (credentials: ['ssh-app01-1']) {
                    sh '''
                        ansible-playbook -i ~/workspace/ansible-pipeline/hosts.yml -l webserver ~/workspace/ansible-pipeline/playbooks/docker-build.yml
                    '''
                }

            }

        }
        
    }
}
