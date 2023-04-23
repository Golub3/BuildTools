class Configuration{
    static String gitRepo = 'https://github.com/Golub3/BuildTools.git'
    static String qaJobName = 'qaJob'
    static String prodJobName = 'prodJob'
}

def printJobName() {
    bat "echo 'Invoking job ${params.nameOfJob}'"
}

def cloneCodeFromRepo() {
    bat "echo '*************** Cloning code ***************'"
    git Configuration.gitRepo
}

def runWithSelectedBuildTool() {
    bat "echo '*************** Building project with ${params.selectedBuildTool} ***************'"
    bat "cd"
    if (params.selectedBuildTool == 'Maven') {
        buildProjectUsingMaven()
    } else {
        buildPrjectUsingGradle()
    }
}

def buildProjectUsingMaven(){
    echo '---------------- Building using Maven ----------------'
    bat "mvn clean package -DskipTests"
}

def buildPrjectUsingGradle(){
    echo '---------------- Building using Gradle ----------------'
    bat "gradle clean assemble"
    // can use gradle clean build for unit test
}

def testWithSelectedBuildTool() {
    bat "echo '*************** Running test with ${params.selectedBuildTool} ***************'"
    bat "cd"
    if (params.selectedBuildTool == 'Maven') {
        testProjectUsingMaven()
    } else {
        testPrjectUsingGradle()
    }
}

def testProjectUsingMaven(){
    bat "echo '---------------- Testing using Maven ----------------'"
    bat "mvn test"
}

def testPrjectUsingGradle(){
    bat "echo '---------------- Testing using Gradle ----------------'"
    bat "gradle test"
}

def runQaProcess(){
    bat "echo '*************** Running QA process ***************'"
    if( params.separatedJob == 'Yes'){
        build job: Configuration.qaJobName 
    } else {
        bat "echo '---------------- Running QA process ----------------'"       
    }   
}

def runProdProcess(){
    bat "echo '*************** Running Production process ***************'"
    if( params.separatedJob == 'Yes'){
        build job: Configuration.prodJobName 
    } else {
         bat "echo '---------------- Running Production process ----------------'"            
    }  
}


pipeline {
    agent any

    parameters {
        choice(name: 'selectedBuildTool', choices: ['Maven', 'Gradle'], description: 'Select build tool')
        string(name: 'nameOfJob', defaultValue: 'Builder Application', description: 'Name of the job. Default is BuilderApp')
        choice(name: 'separatedJob', choices: ['No', 'Yes'], description: 'Call separated job for QA and Prod or not')
    }

    tools {        
        maven 'Maven'
        gradle 'Gradle'
    }

    stages {
        stage ('Preparation') {
            steps {
                printJobName()
                cloneCodeFromRepo()
            }
        }
        stage('Build') {
            steps {
                runWithSelectedBuildTool()
            }
        }
        stage('Test') {
            steps {
                testWithSelectedBuildTool()
            }
        }        
        stage('QA Step') {
            steps {
                runQaProcess()
            }
        }
        stage('Production Step') {
            steps {
                input message: "Approve to Production?"
                runProdProcess()
            }
        } 
    }
}
