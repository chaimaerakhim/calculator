pipeline {
agent any
// agent{
//         docker {
//             image 'node:10.15.3'
//             args '-u root'
//         }
// }

    parameters {
        booleanParam(defaultValue: true, description: 'Build', name: 'BUILD')
        booleanParam(defaultValue: true, description: 'Test', name: 'TEST')
        booleanParam(defaultValue: true, description: 'Test', name: 'DEPLOY')
        //gitParameter branch: '', branchFilter: '.*', defaultValue: 'master', description: 'Branch to build or deploy', name: 'BRANCH_', quickFilterEnabled: true, selectedValue: 'NONE', sortMode: 'NONE', tagFilter: '*', type: 'PT_BRANCH', useRepository: 'https://gitlab.rabat.sqli.com/crakhim/occ.git' 

    }

    triggers {
        gitlab(
          triggerOnPush: true, 
          branchFilterType: 'All',
          secretToken: 'e8ba4672378370c6e7c76f3fc9d773f2'
        )
    }
    stages {
    stage("Install"){
        steps{
            sh '''
            
            docker run --rm -u root  -w /home/node -v $(pwd):/home/node/ node:10.15.3 npm install 
            #docker top occ_install -eo pid,comm

            '''

        }

    }

    stage("Build"){
        when{
            expression { params.BUILD }
        }
        steps{
            sh '''
             docker run -u root -w /home/node -v $(pwd):/home/node/  node:10.15.3 npm run build 

            '''
        }

    }

    stage("Test"){
        when{
            expression { params.TEST }
        }
        steps{

            sh '''
  
            docker run -u root -w /home/node -e CI=true -v $(pwd):/home/node/  node:10.15.3  npm run test 
            #npm run lint
            #npm run test --no-watch


            '''

        }
    }

    stage("Deploy"){
        when{
            expression { params.DEPLOY }
        }
        steps{

            sh '''
            docker rm -f calculator-deploy || true 
            #docker run -d --name occ-deploy -p 8082:80 -v $(pwd)/build:/usr/local/apache2/htdocs/occ-to-react -v $(pwd)/node_modules:/usr/local/apache2/htdocs/occ-to-react/node_modules httpd:2.4 
            docker run -d --name calculator-deploy -p 8082:80 -v $(pwd)/build:/usr/local/apache2/htdocs/ -v $(pwd)/node_modules:/usr/local/apache2/htdocs/node_modules httpd:2.4 

            #docker exec -id occ_deploy_httpd systemctl restart httpd
            '''
        }       

    }


    }


}