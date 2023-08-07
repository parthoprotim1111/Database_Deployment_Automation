node {
    def awsCliPath = env.AWS_CLI

    withCredentials([string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                     string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')]) {

        def DB_INSTANCE_IDENTIFIER = 'mydbinstance'
        def DB_NAME = 'mydatabase'
        def DB_USERNAME = 'myuser'
        def DB_PASSWORD = 'mypassword'
        def DB_PORT = '5432'
        def SCHEMA_NAME = 'myschema'
        def AWS_REGION = 'ap-south-1'

        stage('Create RDS Instance') {
            sh """${awsCliPath}/bin/aws rds create-db-instance \\
                --db-instance-identifier ${DB_INSTANCE_IDENTIFIER} \\
                --allocated-storage 20 \\
                --db-instance-class db.t3.micro \\
                --engine postgres \\
                --engine-version 13.10 \\
                --master-username ${DB_USERNAME} \\
                --master-user-password ${DB_PASSWORD} \\
                --port ${DB_PORT} \\
                --no-publicly-accessible \\
                --no-multi-az \\
                --no-auto-minor-version-upgrade \\
                --no-copy-tags-to-snapshot \\
                --output json
            """
        }

        stage('Wait for RDS Instance') {
            sh """${awsCliPath}/bin/aws rds wait db-instance-available \\
                --db-instance-identifier ${DB_INSTANCE_IDENTIFIER}
            """
        }

        stage('Create Schema') {
            sh """PGPASSWORD=${DB_PASSWORD} ${awsCliPath}/bin/psql \\
                --host=${DB_INSTANCE_IDENTIFIER}.${AWS_REGION}.rds.amazonaws.com \\
                --port=${DB_PORT} \\
                --username=${DB_USERNAME} \\
                --dbname=${DB_NAME} \\
                -c "CREATE SCHEMA ${SCHEMA_NAME}"
            """
        }

        stage('Delete RDS Instance') {
            sh """${awsCliPath}/bin/aws rds delete-db-instance \\
                --db-instance-identifier ${DB_INSTANCE_IDENTIFIER} \\
                --skip-final-snapshot
            """
        }
    }
}
