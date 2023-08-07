node {
    // Define environment variables
    def DB_INSTANCE_IDENTIFIER = "my-db-instance"
    def DB_SCHEMA_NAME = "my_schema"
    
    // Stage: Checkout
    stage('Checkout') {
        // Checkout the code from GitHub repository
        checkout scm
    }
    
    // Stage: Create RDS Instance
    stage('Create RDS Instance') {
        // Create RDS instance using AWS CLI or SDK
        sh 'aws rds create-db-instance --db-instance-identifier $DB_INSTANCE_IDENTIFIER --engine postgres --engine-version 13.0 --allocated-storage 20 --db-instance-class db.t2.micro'
    }
    
    // Stage: Wait for RDS Instance
    stage('Wait for RDS Instance') {
        // Wait for RDS instance to be available
        sh 'aws rds wait db-instance-available --db-instance-identifier $DB_INSTANCE_IDENTIFIER'
    }
    
    
    // Stage: Create Schema
stage('Create Schema') {
    withCredentials([usernamePassword(credentialsId: 'my-db-credentials', usernameVariable: 'DB_USERNAME', passwordVariable: 'DB_PASSWORD')]) {
        sh "psql -h database-1.cyhjwoyrhm6n.us-east-1.rds.amazonaws.com -U $DB_USERNAME -d database-1 -c 'CREATE SCHEMA $DB_SCHEMA_NAME'"
    }
}

    
    // Stage: Run Tests
    stage('Run Tests') {
        // Activate virtual environment and run tests
        sh './run_tests.sh'
    }
    
    // Stage: Clean Up
    stage('Clean Up') {
        // Clean up resources (e.g., delete RDS instance, parameter groups, etc.)
        sh 'aws rds delete-db-instance --db-instance-identifier $DB_INSTANCE_IDENTIFIER --skip-final-snapshot'
    }
}
