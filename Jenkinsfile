pipeline {
    agent any
    environment {
        DB_URL = 'mysql+pymysql://usr:pwd@host:<port>/db'
        DISABLE_AUTH = true
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']],
                          userRemoteConfigs: [[url: 'https://github.com/FraxinRus/MRPP4.git']]
                ])
            }
        }
        stage('Клонирование репозитория') {
            steps {
                echo 'Клонирование репозитория из GitHub...'
                git 'https://github.com/FraxinRus/MRPP4.git'
            }
        }
        stage('Сборка') {
            steps {
                echo 'Сборка приложения...'
                sh '''
                echo "Этот блок содержит многострочные шаги"
                ls -lh
                '''
                echo "URL базы данных: ${DB_URL}"
                echo "DISABLE_AUTH: ${DISABLE_AUTH}"
                echo "Запуск задачи с номером сборки: ${env.BUILD_NUMBER} на ${env.JENKINS_URL}"
            }
        }
        stage('Тестирование') {
            steps {
                echo 'Тестирование приложения...'
            }
        }
        stage('Деплой на стейджинг') {
            steps {
                echo 'Подготовка к деплою на стейджинг...'
                sh 'chmod +x deploy smoke-tests'
                sh './deploy staging'
                sh './smoke-tests'
            }
        }
        stage('Проверка работоспособности') {
            steps {
                input message: 'Следует ли отправить на продакшн?', ok: 'Да'
            }
        }
        stage('Деплой на продакшн') {
            steps {
                echo 'Деплой на продакшн...'
                sh './deploy prod'
            }
        }
    }
    post {
        always {
            echo 'Всегда выполняется, независимо от результата'
        }
        success {
            echo 'Сборка завершилась успешно!'
        }
        failure {
            echo 'Сборка провалилась. Проверьте логи для деталей.'
        }
        cleanup {
            echo 'Очистка рабочей области...'
            cleanWs()
        }
    }
}
