node {
    def awsCliPath = env.AWS_CLI
    def psqlPath = '/usr/bin'  // Update this to the correct path of the psql executable

    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'AWS_Access', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {

        def DB_INSTANCE_IDENTIFIER = 'mydbinstance'
        def DB_NAME = 'mydatabase'
        def DB_USERNAME = 'myuser'
        def DB_PASSWORD = 'mypassword'
        def DB_PORT = '5432'
        def SCHEMA_NAME = 'myschema'
        def AWS_REGION = 'ap-south-1'

        stage('Delete Existing RDS Instance') {
            sh """${awsCliPath}/aws rds delete-db-instance \\
                --db-instance-identifier ${DB_INSTANCE_IDENTIFIER} \\
                --skip-final-snapshot
            """
        }

        stage('Wait for Instance Deletion') {
            sh """${awsCliPath}/aws rds wait db-instance-deleted \\
                --db-instance-identifier ${DB_INSTANCE_IDENTIFIER}
            """
        }

        stage('Create RDS Instance') {
            sh """${awsCliPath}/aws rds create-db-instance \\
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
            sh """${awsCliPath}/aws rds wait db-instance-available \\
                --db-instance-identifier ${DB_INSTANCE_IDENTIFIER}
            """
        }

        stage('Create Schema') {
            sh """PGPASSWORD=${DB_PASSWORD} ${psqlPath}/psql \\
                --host=${DB_INSTANCE_IDENTIFIER}.${AWS_REGION}.rds.amazonaws.com \\
                --port=${DB_PORT} \\
                --username=${DB_USERNAME} \\
                --dbname=${DB_NAME} \\
                -c "CREATE SCHEMA ${SCHEMA_NAME}"
            """
        }

        stage('Delete RDS Instance') {
            sh """${awsCliPath}/aws rds delete-db-instance \\
                --db-instance-identifier ${DB_INSTANCE_IDENTIFIER} \\
                --skip-final-snapshot
            """
        }
    }
}
