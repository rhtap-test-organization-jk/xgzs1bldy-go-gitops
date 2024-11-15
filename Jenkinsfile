/* Generated from templates/gitops-template/Jenkinsfile.njk. Do not edit directly. */

library identifier: 'RHTAP_Jenkins@main', retriever: modernSCM(
  [$class: 'GitSCMSource',
   remote: 'https://github.com/redhat-appstudio/tssc-sample-jenkins.git'])

pipeline {
    agent {
      kubernetes {
        label 'jenkins-agent'
        cloud 'openshift'
        serviceAccount 'jenkins'
        podRetention onFailure()
        idleMinutes '5'
        containerTemplate {
         name 'jnlp'
         image 'quay.io/jkopriva/rhtap-jenkins-agent:0.1'
         ttyEnabled true
         args '${computer.jnlpmac} ${computer.name}'
        }
       }
}
    environment {
        /* Not used but init.sh will fail if they're missing */
        COSIGN_SECRET_PASSWORD = 'dummy'
        COSIGN_SECRET_KEY = 'dummy'
        /* Used to verify the image signature and attestation */
        COSIGN_PUBLIC_KEY = credentials('COSIGN_PUBLIC_KEY')
    }
    stages {
        stage('Verify EC') {
            steps {
                script {
                    rhtap.info('gather_deploy_images')
                    rhtap.gather_deploy_images()
                    rhtap.info('verify_enterprise_contract')
                    rhtap.verify_enterprise_contract()
                }
            }
        }

        stage('Upload SBOM') {
            steps {
                script {
                    rhtap.info('gather_images_to_upload_sbom')
                    rhtap.gather_images_to_upload_sbom()
                    rhtap.info('download_sbom_from_url_in_attestation')
                    rhtap.download_sbom_from_url_in_attestation()
                }
            }
        }

    }
}
