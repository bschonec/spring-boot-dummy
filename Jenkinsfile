echo "Branch name: ${env.BRANCH_NAME}"
BUILD = 'master'
echo "Build: ${BUILD}"

node ('master') {
    checkout scm
    echo "Build: ${BUILD}"
    stage ('Build Building container') {
        echo "Build: ${BUILD}"
        sh "docker build -t laszlocph/build-spring-boot-dummy ."
    }
    stage ('Build in Container') {
        def CONTAINER_ID = sh (
                script: "docker run -d --network=host laszlocph/build-spring-boot-dummy:${BUILD}",
                returnStdout: true
        ).trim()
        echo "Container id: ${CONTAINER_ID}"

        sh "docker exec ${CONTAINER_ID} ./gradlew build"
        sh "docker cp ${CONTAINER_ID}:/spring-boot-dummy/build/libs/spring-boot-dummy-0.1.0.jar docker/spring-boot-dummy-0.1.0.jar"
        sh "docker stop ${CONTAINER_ID}"
        sh "docker rm ${CONTAINER_ID}"
    }

    stage ('Build Container with latest Build') {
        dir('docker') {
            sh "docker build -t laszlocph/spring-boot-dummy ."
            sh "docker push laszlocph/spring-boot-dummy"
        }
    }
}
