try {
   timeout(time: 20, unit: 'MINUTES') {
      def appName="cotd"
      def project=""
      def tag="cats"
      def verbose="false"

      node {
        project = env.PROJECT_NAME
        stage("Initialize") {
          sh "oc get route ${appName} -n ${project} -o jsonpath='{ .spec.to.name }' --loglevel=4 > activeservice"
          activeService = readFile('activeservice').trim()
        }

        stage("Build") {
          echo "building tag ${tag}"
          openshiftBuild buildConfig: appName, showBuildLogs: "true", verbose: verbose, env: [[name: 'SELECTOR', value: tag]]
        }

        stage("Deploy Test") {
          openshiftTag srcStream: appName, srcTag: 'latest', destinationStream: appName, destinationTag: tag, verbose: verbose
          openshiftVerifyDeployment deploymentConfig: "${appName}", verbose: verbose
        }
      }
   }
} catch (err) {
   echo "in catch block"
   echo "Caught: ${err}"
   currentBuild.result = 'FAILURE'
   throw err
}
