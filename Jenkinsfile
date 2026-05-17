pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS' 
    }

    stages {
        stage('Instalacion de dependencias') {
            steps {
                echo 'Instalando dependencias de Node...'
                sh 'npm install' 
            }
        }
        stage('Analisis de codigo (lint)') {
            steps {
                echo 'Comprobando el formato del codigo...'
                sh 'npm run lint || true'
            }
        }
        stage('Ejecucion de tests (basicos)') {
            steps {
                echo 'Ejecutando tests de prueba...'
                sh 'npm run test:unit || true'
            }
        }
        stage('Build del proyecto') {
            steps {
                echo 'Compilando el proyecto Vue para produccion...'
                sh 'npm run build'
            }
        }
        stage('Deploy automatico en Firebase') {
            steps {
                echo 'Instalando herramientas de Firebase y subiendo...'
                sh 'npm install firebase-tools'
                sh 'npx firebase deploy --only hosting --token $FIREBASE_TOKEN'
            }
        }
    }
}