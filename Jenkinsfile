pipeline {
    agent any
    environment {
        // Set up Environment Variables
        //CHANGE THIS SECTION FOR EACH SYSTEM 
        // OR set up a secret text credential for each of the variables.
        ENDEVOR=" --port 7080 --protocol http --reject-unauthorized false -i NDVRWEBS --comment SQScan --ccid SQSCAN"

        ZOWE_OPT_HOSTNAME="mstrsvw.lvn.broadcom.net"
        ZOWE_OPT_HOST="$ZOWE_OPT_HOSTNAME"

        // z/OSMF Connection Details
        ZOWE_OPT_PORT="443"
        ZOWE_OPT_REJECT_UNAUTHORIZED=false

    }
    stages {
        stage('Download Code') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'mcquitty', usernameVariable: 'ZOWE_OPT_USER', passwordVariable: 'ZOWE_OPT_PASSWORD')]) {
                    // Jenkins runs as a different user.  Uncomment lines below to install plugin. Other plugins could be added here.
                    bat "npm config set @brightside:registry https://api.bintray.com/npm/ca/brightside"
                    bat "C:/Users/Administrator/AppData/Roaming/npm/zowe.cmd plugins install @brightside/endevor@lts-incremental"
                    bat "C:/Users/Administrator/AppData/Roaming/npm/zowe.cmd endevor retrieve element $elementname --env $toenvironment --sn $tostageid --sys $tosystem --sub $tosubsystem --typ $totype --tf $elementname.$TOTYPE $ENDEVOR"
                }
            }
        }
        stage('SonarQube Scan') {
            steps {
                    // Starts the SonarQube Scan
                    // We want to ensure these directories exist, however if the call fails, we are ok with that.
                    // Hence the returnStatus always returns true.  The BAT function only works on windows, I believe.
                    // use SH for linux/mac and change the scanner function.  This could also use the scanner plugin.
                    bat returnStatus: true, script: 'mkdir cobol'
                    bat returnStatus: true, script: 'mkdir copybooks'
                    bat returnStatus: true, script: 'move /y *.COBOL ./cobol'
                    bat returnStatus: true, script: 'move /y *.cpy ./copybooks'
                    bat "c:\\sonar-scanner\\bin\\sonar-scanner.bat"
            }
        }
        stage('Clean up') {
            steps {
                echo "Cleaning up..."
                    bat returnStatus: true, script: 'del end*.txt'
            }    
        }
    }
}
