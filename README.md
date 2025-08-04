🧠 Self-Healing DevOps Agent (AutoFix CI/CD)
A LangGraph-based AI agent system that autonomously detects, understands, and fixes CI/CD failures in GitHub Actions using LLM Model.

🎓 What It Does
  •	Detects failed GitHub Actions workflows
  •	Uses LLM to suggest contextual fixes
  •	Optionally posts PR suggestions or comments
  •	Stores prior issues in a vector DB for reuse

📊 Architecture
  •	fetch_ci_failure: pulls job & step failures from GitHub
  •	suggest_fix: formats logs + workflows and prompts GPT-4
  •	validate_fix: checks for YAML syntax, dry-run results
  •	memory_search: retrieves similar errors and past solutions

🧩 LangGraph Architecture Diagram

<img width="302" height="648" alt="image" src="https://github.com/user-attachments/assets/b04f3b4c-33e3-4390-99c7-fce7d7b13256" />

🧠 Agent Flow Diagram

<img width="468" height="22" alt="image" src="https://github.com/user-attachments/assets/0cfc0d04-1c89-4775-913c-702d8a095001" />

🔁 CI/CD Healing Lifecycle Illustration
sequenceDiagram

<img width="468" height="224" alt="image" src="https://github.com/user-attachments/assets/7e5322ff-30c7-4c85-9834-5f01aa74dc98" />

📦 Tech Stack
  •	LangGraph
  •	OpenAI GPT-4
  •	GitHub REST API







erDiagram
    %% Core Error Storage
    ERRORS {
        int error_id PK
        string error_fingerprint UK "Unique error pattern hash"
        string source_platform "Jenkins/GitHub/etc"
        string error_type "Build/Pipeline/Workflow"
        text error_message
        text stack_trace
        text error_context
        datetime created_at
        datetime updated_at
        string classification "Auto-classified category"
        float confidence_score "Pattern matching confidence"
    }

    %% Solution Storage
    SOLUTIONS {
        int solution_id PK
        int error_id FK
        text solution_steps "Step-by-step fix guide"
        text code_examples "Sample code/commands"
        text explanation "LLM generated reasoning"
        string solution_source "Database/LLM/Manual"
        float success_rate "Historical success percentage"
        int usage_count "How many times used"
        datetime created_at
        datetime last_used_at
        boolean is_validated "Quality checked"
    }

    %% Pattern Matching for Similar Errors
    ERROR_PATTERNS {
        int pattern_id PK
        string pattern_hash UK "Normalized pattern identifier"
        text pattern_template "Template for matching"
        string keywords "Searchable terms"
        text metadata "Additional matching criteria"
        int match_count "How often this pattern matched"
        datetime created_at
        datetime updated_at
    }

    %% Link Errors to Patterns (Many-to-Many)
    ERROR_PATTERN_LINKS {
        int link_id PK
        int error_id FK
        int pattern_id FK
        float similarity_score "How well error matches pattern"
        datetime created_at
    }

    %% Build/Pipeline Context
    BUILD_CONTEXTS {
        int context_id PK
        int error_id FK
        string job_name "Jenkins job/GitHub workflow"
        string branch_name
        string commit_hash
        text build_logs "Relevant log excerpts"
        string environment "dev/staging/prod"
        json build_metadata "Additional context data"
        datetime build_timestamp
    }

    %% LLM Interactions Log
    LLM_INTERACTIONS {
        int interaction_id PK
        int error_id FK
        text prompt_sent "What was sent to LLM"
        text llm_response "Raw LLM response"
        string llm_model "GPT-4/Claude/etc"
        float processing_time "Response time in seconds"
        datetime created_at
        boolean was_successful "Did LLM provide valid solution"
    }

    %% Metrics and Analytics
    RESOLUTION_METRICS {
        int metric_id PK
        int error_id FK
        int solution_id FK
        string resolution_status "Found/Generated/Failed"
        float lookup_time "Time to find in database"
        float llm_time "Time for LLM processing"
        float total_time "End-to-end processing time"
        datetime resolution_timestamp
        string user_feedback "Success/Failure feedback"
    }

    %% Simple User Feedback
    SOLUTION_FEEDBACK {
        int feedback_id PK
        int solution_id FK
        string feedback_type "Helpful/Not Helpful/Needs Update"
        text comments "Optional user comments"
        datetime created_at
        string user_identifier "Who provided feedback"
    }

    %% Relationships
    ERRORS ||--o{ SOLUTIONS : "has"
    ERRORS ||--o{ ERROR_PATTERN_LINKS : "matches"
    ERROR_PATTERNS ||--o{ ERROR_PATTERN_LINKS : "contains"
    ERRORS ||--|| BUILD_CONTEXTS : "occurred_in"
    ERRORS ||--o{ LLM_INTERACTIONS : "processed_by"
    ERRORS ||--o{ RESOLUTION_METRICS : "tracked_in"
    SOLUTIONS ||--o{ RESOLUTION_METRICS : "measured_by"
    SOLUTIONS ||--o{ SOLUTION_FEEDBACK : "receives"








    This database schema captures the key components from your CI/CD error resolution system flowcharts:
Core Tables:

ERRORS: Stores the raw error data from Jenkins/GitHub with classification and fingerprinting
SOLUTIONS: Contains both database-found and LLM-generated solutions with success tracking
ERROR_PATTERNS: Enables pattern matching for similar errors (the 80% confidence check from your flow)

Key Features:

Pattern Matching: The ERROR_PATTERNS and ERROR_PATTERN_LINKS tables support the similarity search and confidence scoring mentioned in your diagrams
LLM Integration: LLM_INTERACTIONS table logs all AI processing for debugging and improvement
Learning Loop: The schema supports storing new patterns and solutions back to the database as shown in your "learning loop" components
Metrics Tracking: Comprehensive tracking of resolution times, success rates, and user feedback for your pilot metrics

Pilot-Friendly Design:

Simple structure that can start with 50-100 error patterns as mentioned in your diagram
Tracks both "found vs created" solutions for your pilot metrics
Supports the 4-phase rollout (Basic Integration → Smart Lookup → AI Generation → Self-Learning)

The schema directly maps to your flowchart components like the Error Database, Lookup Engine, Solution Validator, and Database Updater, making it easy to implement the system architecture you've designed.




I've created a complete database structure with all the required tables and populated each with 10 realistic sample records. Here's what I've included:
Database Structure:

ERRORS - Core error storage with fingerprinting and classification
SOLUTIONS - Both database-found and LLM-generated solutions
ERROR_PATTERNS - Pattern templates for similarity matching
ERROR_PATTERN_LINKS - Many-to-many relationships between errors and patterns
BUILD_CONTEXTS - CI/CD job context and metadata
LLM_INTERACTIONS - Complete LLM processing logs
RESOLUTION_METRICS - Performance and success tracking
SOLUTION_FEEDBACK - User feedback for continuous improvement

Sample Data Highlights:

Realistic CI/CD Errors: NPM failures, Docker build issues, Maven compilation errors, Python import problems, etc.
Multiple Platforms: Jenkins and GitHub Actions examples
Solution Sources: Mix of database-found, LLM-generated, and manual solutions
Success Metrics: Realistic success rates, usage counts, and processing times
Rich Context: Actual commit hashes, build logs, environment details

Performance Features:

Indexes on frequently queried columns
Views for common queries (error summaries, top solutions)
Constraints for data integrity
JSON support for flexible metadata storage

This structure directly supports your flowchart's components:

Error Collector → ERRORS table
Pattern Matching → ERROR_PATTERNS + ERROR_PATTERN_LINKS
LLM Engine → LLM_INTERACTIONS
Solution Storage → SOLUTIONS table
Learning Loop → All tables working together to improve over time

The sample data represents a realistic pilot phase with common CI/CD issues that your system would encounter in production.RetryS



-- Sample BUILD_CONTEXTS data
INSERT INTO BUILD_CONTEXTS (error_id, job_name, branch_name, commit_hash, build_logs, environment, build_metadata, build_timestamp) VALUES
(1, 'frontend-build-pipeline', 'feature/user-dashboard', 'a1b2c3d4e5f678901234567890abcdef12345678', 'npm WARN deprecated package@1.0.0\nnpm ERR! peer dep missing', 'dev', '{"node_version": "18.12.0", "npm_version": "8.19.2"}', '2024-07-28 09:15:23'),
(2, 'api-docker-build', 'main', 'b2c3d4e5f6789012345678901abcdef234567890', 'Step 3/8 : FROM node:18-alpine\npull access denied', 'staging', '{"docker_version": "20.10.17", "buildkit": true}', '2024-07-28 10:22:45-- ================================================
-- CI/CD Error Resolution System Database Structure
-- ================================================

-- Core Error Storage Table
CREATE TABLE ERRORS (
    error_id SERIAL PRIMARY KEY,
    error_fingerprint VARCHAR(64) UNIQUE NOT NULL,
    source_platform VARCHAR(50) NOT NULL,
    error_type VARCHAR(50) NOT NULL,
    error_message TEXT NOT NULL,
    stack_trace TEXT,
    error_context TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    classification VARCHAR(100),
    confidence_score DECIMAL(3,2)
);

-- Solution Storage Table
CREATE TABLE SOLUTIONS (
    solution_id SERIAL PRIMARY KEY,
    error_id INTEGER REFERENCES ERRORS(error_id),
    solution_steps TEXT NOT NULL,
    code_examples TEXT,
    explanation TEXT,
    solution_source VARCHAR(20) NOT NULL CHECK (solution_source IN ('Database', 'LLM', 'Manual')),
    success_rate DECIMAL(5,2) DEFAULT 0.0,
    usage_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_used_at TIMESTAMP,
    is_validated BOOLEAN DEFAULT FALSE
);

-- Error Pattern Matching Table
CREATE TABLE ERROR_PATTERNS (
    pattern_id SERIAL PRIMARY KEY,
    pattern_hash VARCHAR(64) UNIQUE NOT NULL,
    pattern_template TEXT NOT NULL,
    keywords TEXT,
    metadata TEXT,
    match_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Many-to-Many Link Between Errors and Patterns
CREATE TABLE ERROR_PATTERN_LINKS (
    link_id SERIAL PRIMARY KEY,
    error_id INTEGER REFERENCES ERRORS(error_id),
    pattern_id INTEGER REFERENCES ERROR_PATTERNS(pattern_id),
    similarity_score DECIMAL(3,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Build Context Information
CREATE TABLE BUILD_CONTEXTS (
    context_id SERIAL PRIMARY KEY,
    error_id INTEGER REFERENCES ERRORS(error_id),
    job_name VARCHAR(200) NOT NULL,
    branch_name VARCHAR(100),
    commit_hash VARCHAR(40),
    build_logs TEXT,
    environment VARCHAR(20) NOT NULL CHECK (environment IN ('dev', 'staging', 'prod')),
    build_metadata JSON,
    build_timestamp TIMESTAMP NOT NULL
);

-- LLM Interaction Logging
CREATE TABLE LLM_INTERACTIONS (
    interaction_id SERIAL PRIMARY KEY,
    error_id INTEGER REFERENCES ERRORS(error_id),
    prompt_sent TEXT NOT NULL,
    llm_response TEXT,
    llm_model VARCHAR(50) NOT NULL,
    processing_time DECIMAL(6,3),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    was_successful BOOLEAN DEFAULT FALSE
);

-- Resolution Metrics and Analytics
CREATE TABLE RESOLUTION_METRICS (
    metric_id SERIAL PRIMARY KEY,
    error_id INTEGER REFERENCES ERRORS(error_id),
    solution_id INTEGER REFERENCES SOLUTIONS(solution_id),
    resolution_status VARCHAR(20) NOT NULL CHECK (resolution_status IN ('Found', 'Generated', 'Failed')),
    lookup_time DECIMAL(6,3),
    llm_time DECIMAL(6,3),
    total_time DECIMAL(6,3),
    resolution_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    user_feedback VARCHAR(50)
);

-- User Feedback on Solutions
CREATE TABLE SOLUTION_FEEDBACK (
    feedback_id SERIAL PRIMARY KEY,
    solution_id INTEGER REFERENCES SOLUTIONS(solution_id),
    feedback_type VARCHAR(20) NOT NULL CHECK (feedback_type IN ('Helpful', 'Not Helpful', 'Needs Update')),
    comments TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    user_identifier VARCHAR(100)
);

-- ================================================
-- SAMPLE DATA INSERTION
-- ================================================

-- Sample ERRORS data
INSERT INTO ERRORS (error_fingerprint, source_platform, error_type, error_message, stack_trace, error_context, classification, confidence_score) VALUES
('a1b2c3d4e5f6', 'Jenkins', 'Build', 'npm install failed with exit code 1', 'Error: Cannot resolve dependency @angular/core@^15.0.0', 'Node.js project build step', 'Dependency Resolution', 0.95),
('f6e5d4c3b2a1', 'GitHub Actions', 'Workflow', 'Docker build failed: base image not found', 'Error: pull access denied for node:18-alpine', 'Docker containerization step', 'Docker Configuration', 0.92),
('1a2b3c4d5e6f', 'Jenkins', 'Pipeline', 'Maven compilation error', 'compilation failure: package com.example does not exist', 'Java project compilation', 'Java Compilation', 0.89),
('6f5e4d3c2b1a', 'GitHub Actions', 'Action', 'Python tests failed with ImportError', 'ImportError: No module named pytest', 'Python test execution', 'Python Dependencies', 0.91),
('b1c2d3e4f5g6', 'Jenkins', 'Build', 'Gradle build timeout', 'Build timed out after 30 minutes', 'Long-running Gradle task', 'Build Performance', 0.87),
('g6f5e4d3c2b1', 'GitHub Actions', 'Workflow', 'Database migration failed', 'ActiveRecord::PendingMigrationError', 'Rails application deployment', 'Database Migration', 0.94),
('c1d2e3f4g5h6', 'Jenkins', 'Pipeline', 'ESLint validation failed', 'ESLint found 15 errors in src/components/', 'Code quality check', 'Code Quality', 0.88),
('h6g5f4e3d2c1', 'GitHub Actions', 'Action', 'Kubernetes deployment timeout', 'deployment "api-service" exceeded progress deadline', 'K8s deployment step', 'Kubernetes Deployment', 0.93),
('d1e2f3g4h5i6', 'Jenkins', 'Build', 'Unit tests failed', '5 tests failed in UserServiceTest.java', 'JUnit test execution', 'Unit Testing', 0.90),
('i6h5g4f3e2d1', 'GitHub Actions', 'Workflow', 'Terraform plan failed', 'Error: Invalid provider configuration', 'Infrastructure provisioning', 'Infrastructure as Code', 0.86);

-- Sample SOLUTIONS data
INSERT INTO SOLUTIONS (error_id, solution_steps, code_examples, explanation, solution_source, success_rate, usage_count, is_validated) VALUES
(1, '1. Delete node_modules and package-lock.json\n2. Run npm cache clean --force\n3. Run npm install', 'rm -rf node_modules package-lock.json\nnpm cache clean --force\nnpm install', 'Common npm dependency resolution issue caused by corrupted cache', 'Database', 85.5, 12, TRUE),
(2, '1. Update Dockerfile base image\n2. Use official Node.js image\n3. Rebuild container', 'FROM node:18-alpine\n# Change to:\nFROM node:18', 'Docker Hub rate limiting or incorrect image tag', 'LLM', 92.0, 8, TRUE),
(3, '1. Check import statements\n2. Verify package structure\n3. Update Maven dependencies', '<dependency>\n  <groupId>com.example</groupId>\n  <artifactId>core</artifactId>\n</dependency>', 'Missing Maven dependency in pom.xml', 'Database', 78.3, 15, TRUE),
(4, '1. Install pytest in requirements.txt\n2. Update GitHub Actions workflow\n3. Activate virtual environment', 'pip install pytest\n# Add to requirements.txt:\npytest>=7.0.0', 'Missing test dependency in Python project', 'Database', 91.2, 22, TRUE),
(5, '1. Increase build timeout\n2. Enable Gradle daemon\n3. Add parallel execution', 'timeout: 60m\n# In gradle.properties:\norg.gradle.daemon=true\norg.gradle.parallel=true', 'Gradle build performance optimization needed', 'LLM', 73.8, 6, FALSE),
(6, '1. Run pending migrations\n2. Check database connection\n3. Update schema version', 'rails db:migrate\nrails db:migrate:status', 'Database schema out of sync with application code', 'Database', 88.7, 18, TRUE),
(7, '1. Fix ESLint errors\n2. Update ESLint configuration\n3. Use --fix flag for auto-fixes', 'npx eslint src/ --fix\n# Update .eslintrc.js with proper rules', 'Code style violations preventing build', 'Manual', 95.1, 31, TRUE),
(8, '1. Increase deployment timeout\n2. Check resource limits\n3. Verify image availability', 'kubectl patch deployment api-service -p \'{"spec":{"progressDeadlineSeconds":900}}\'', 'Kubernetes deployment taking longer than expected', 'LLM', 67.4, 4, FALSE),
(9, '1. Review failing test cases\n2. Update test assertions\n3. Mock external dependencies', '@Test\npublic void testUserCreation() {\n  // Updated test logic\n}', 'Unit tests failing due to changed business logic', 'Manual', 82.9, 19, TRUE),
(10, '1. Validate Terraform syntax\n2. Check provider versions\n3. Initialize Terraform', 'terraform init\nterraform validate\nterraform plan', 'Terraform configuration syntax or provider issues', 'Database', 76.5, 11, TRUE);

-- Sample ERROR_PATTERNS data
INSERT INTO ERROR_PATTERNS (pattern_hash, pattern_template, keywords, metadata, match_count) VALUES
('npm_install_fail_001', 'npm install failed with exit code {code}', 'npm, install, exit code, dependency', 'Node.js build failures related to package installation', 25),
('docker_build_fail_001', 'Docker build failed: {image} not found', 'docker, build, image, not found', 'Docker image pull or build failures', 18),
('maven_compile_fail_001', 'Maven compilation error: package {package} does not exist', 'maven, compilation, package, does not exist', 'Java Maven compilation issues', 32),
('python_import_fail_001', 'ImportError: No module named {module}', 'python, import, module, not found', 'Python dependency and import errors', 28),
('gradle_timeout_001', 'Gradle build timeout after {time}', 'gradle, timeout, build, performance', 'Gradle build performance issues', 12),
('rails_migration_fail_001', 'ActiveRecord::PendingMigrationError', 'rails, migration, database, pending', 'Ruby on Rails database migration issues', 22),
('eslint_fail_001', 'ESLint found {count} errors in {path}', 'eslint, errors, code quality, validation', 'JavaScript/TypeScript code quality issues', 45),
('k8s_deploy_fail_001', 'deployment "{name}" exceeded progress deadline', 'kubernetes, deployment, timeout, deadline', 'Kubernetes deployment timeout issues', 15),
('junit_test_fail_001', '{count} tests failed in {file}', 'junit, tests, failed, unit testing', 'Java unit test failures', 38),
('terraform_fail_001', 'Terraform {operation} failed: {reason}', 'terraform, infrastructure, configuration, error', 'Infrastructure as Code issues', 19),

-- Additional Jenkins & Harness Patterns
('jenkins_disk_fail_001', 'No space left on device', 'jenkins, disk, space, storage, cleanup', 'Jenkins disk space exhaustion issues', 58),
('jenkins_agent_fail_001', 'Agent is offline', 'jenkins, agent, offline, connectivity, node', 'Jenkins build agent connectivity problems', 42),
('jenkins_perm_fail_001', 'Permission denied: {user} cannot {action}', 'jenkins, permission, access, denied, filesystem', 'Jenkins file system permission issues', 67),
('jenkins_memory_fail_001', 'OutOfMemoryError: Java heap space', 'jenkins, memory, heap, outofmemory, jvm', 'Jenkins JVM memory allocation failures', 39),
('jenkins_git_auth_001', 'Git clone failed: Authentication failed', 'jenkins, git, authentication, clone, credentials', 'Jenkins Git authentication and credential issues', 73),
('jenkins_plugin_fail_001', 'Plugin dependency not met', 'jenkins, plugin, dependency, version, compatibility', 'Jenkins plugin compatibility and dependency issues', 31),
('jenkins_port_fail_001', 'Port {port} already in use', 'jenkins, port, bind, address, conflict', 'Jenkins port binding and conflict issues', 24),
('jenkins_ssl_fail_001', 'SSL certificate verification failed', 'jenkins, ssl, certificate, verification, tls', 'Jenkins SSL/TLS certificate validation issues', 29),
('jenkins_script_fail_001', 'Script approval required', 'jenkins, script, approval, security, groovy', 'Jenkins script security and approval issues', 46),
('jenkins_workspace_001', 'Workspace locked by another build', 'jenkins, workspace, locked, concurrent, build', 'Jenkins workspace locking and concurrency issues', 35),
('harness_conn_fail_001', 'Connector authentication failed', 'harness, connector, authentication, kubernetes, credentials', 'Harness connector authentication failures', 52),
('harness_deleg_fail_001', 'Delegate not available', 'harness, delegate, availability, connectivity, task', 'Harness delegate connectivity and availability issues', 48),
('harness_secret_fail_001', 'Secret resolution failed', 'harness, secret, resolution, manager, credentials', 'Harness secret management and resolution issues', 37),
('harness_helm_fail_001', 'Helm chart deployment failed', 'harness, helm, deployment, chart, kubernetes', 'Harness Helm deployment and configuration issues', 41),
('harness_tf_lock_001', 'Terraform state lock timeout', 'harness, terraform, state, lock, timeout', 'Harness Terraform state locking issues', 28),
('harness_approval_001', 'Manual approval timeout', 'harness, approval, manual, timeout, workflow', 'Harness approval workflow and timeout issues', 33),
('harness_artifact_001', 'Artifact download failed', 'harness, artifact, download, registry, timeout', 'Harness artifact registry connectivity issues', 44),
('harness_policy_001', 'Policy evaluation failed', 'harness, policy, opa, evaluation, governance', 'Harness policy governance and OPA evaluation issues', 26),
('harness_infra_001', 'Infrastructure provisioning timeout', 'harness, infrastructure, provisioning, timeout, cloudformation', 'Harness infrastructure provisioning timeout issues', 22),
('harness_rollback_001', 'Rollback failed', 'harness, rollback, deployment, artifact, version', 'Harness deployment rollback and artifact issues', 19);

-- Sample ERROR_PATTERN_LINKS data
INSERT INTO ERROR_PATTERN_LINKS (error_id, pattern_id, similarity_score) VALUES
(1, 1, 0.98),
(2, 2, 0.95),
(3, 3, 0.92),
(4, 4, 0.96),
(5, 5, 0.89),
(6, 6, 0.94),
(7, 7, 0.91),
(8, 8, 0.87),
(9, 9, 0.93),
(10, 10, 0.88),

-- Additional pattern links for Jenkins & Harness issues
(11, 11, 0.97),
(12, 12, 0.94),
(13, 13, 0.91),
(14, 14, 0.96),
(15, 15, 0.93),
(16, 16, 0.89),
(17, 17, 0.92),
(18, 18, 0.88),
(19, 19, 0.95),
(20, 20, 0.87),
(21, 16, 0.85), -- NodeJS issues can match plugin patterns
(22, 14, 0.83), -- Maven/Java issues can match memory patterns
(23, 12, 0.89), -- Docker issues can match agent patterns
(24, 15, 0.91), -- Credential issues match Git auth patterns
(25, 12, 0.86), -- Queue issues match agent patterns
(26, 15, 0.88), -- SCM issues match Git patterns
(27, 13, 0.84), -- Archive issues can match permission patterns
(28, 18, 0.87), -- Email issues can match SSL patterns
(29, 16, 0.90), -- Library issues match plugin patterns
(30, 20, 0.93), -- Resource lock matches workspace patterns
(31, 21, 0.96),
(32, 22, 0.94),
(33, 23, 0.92),
(34, 24, 0.89),
(35, 25, 0.91),
(36, 26, 0.88),
(37, 27, 0.90),
(38, 28, 0.93),
(39, 29, 0.87),
(40, 30, 0.85);

-- Sample BUILD_CONTEXTS data
INSERT INTO BUILD_CONTEXTS (error_id, job_name, branch_name, commit_hash, build_logs, environment, build_metadata, build_timestamp) VALUES
(1, 'frontend-build-pipeline', 'feature/user-dashboard', 'a1b2c3d4e5f678901234567890abcdef12345678', 'npm WARN deprecated package@1.0.0\nnpm ERR! peer dep missing', 'dev', '{"node_version": "18.12.0", "npm_version": "8.19.2"}', '2024-07-28 09:15:23'),
(2, 'api-docker-build', 'main', 'b2c3d4e5f6789012345678901abcdef234567890', 'Step 3/8 : FROM node:18-alpine\npull access denied', 'staging', '{"docker_version": "20.10.17", "buildkit": true}', '2024-07-28 10:22:45'),
(3, 'backend-maven-build', 'develop', 'c3d4e5f6789012345678901abcdef34567890123', '[ERROR] Failed to execute goal org.apache.maven.plugins', 'dev', '{"java_version": "11.0.16", "maven_version": "3.8.6"}', '2024-07-28 11:30:12'),
(4, 'python-test-workflow', 'feature/api-tests', 'd4e5f6789012345678901abcdef4567890123456', 'E   ImportError: No module named pytest\nCollected 0 items', 'dev', '{"python_version": "3.9.13", "pip_version": "22.1.2"}', '2024-07-28 12:45:33'),
(5, 'gradle-build-job', 'release/v2.1', 'e5f6789012345678901abcdef567890123456789', '> Task :compileJava\n> Task :processResources\nBUILD FAILED', 'prod', '{"gradle_version": "7.5.1", "jvm_memory": "4g"}', '2024-07-28 13:20:55'),
(6, 'rails-deploy-pipeline', 'hotfix/db-migration', 'f6789012345678901abcdef67890123456789012', 'Migrating to CreateUserProfiles (20240728000001)\nrails aborted!', 'staging', '{"ruby_version": "3.1.2", "rails_version": "7.0.4"}', '2024-07-28 14:10:17'),
(7, 'frontend-quality-check', 'feature/component-refactor', '789012345678901abcdef7890123456789012345', '/src/components/UserProfile.tsx\n  15:1  error  Missing return type', 'dev', '{"eslint_version": "8.22.0", "typescript_version": "4.8.2"}', '2024-07-28 15:05:41'),
(8, 'k8s-deployment-prod', 'main', '89012345678901abcdef890123456789012345678', 'deployment.apps/api-service condition met\nWaiting for rollout to finish', 'prod', '{"kubectl_version": "1.24.3", "cluster": "prod-cluster"}', '2024-07-28 16:35:29'),
(9, 'backend-unit-tests', 'feature/user-service', '9012345678901abcdef90123456789012345678901', 'Tests run: 25, Failures: 5, Errors: 0, Skipped: 0', 'dev', '{"junit_version": "5.8.2", "mockito_version": "4.6.1"}', '2024-07-28 17:18:52'),
(10, 'infrastructure-terraform', 'feature/new-vpc', '012345678901abcdef0123456789012345678901234', 'Error: Invalid provider configuration for provider[registry.terraform.io/hashicorp/aws]', 'staging', '{"terraform_version": "1.2.9", "aws_provider": "4.28.0"}', '2024-07-28 18:42:16');

-- Sample LLM_INTERACTIONS data
INSERT INTO LLM_INTERACTIONS (error_id, prompt_sent, llm_response, llm_model, processing_time, was_successful) VALUES
(1, 'Analyze this npm install error and provide a solution: npm install failed with exit code 1, Error: Cannot resolve dependency @angular/core@^15.0.0', 'This error indicates a dependency resolution conflict. The solution is to clear the npm cache and reinstall dependencies...', 'GPT-4', 2.345, TRUE),
(2, 'Help fix this Docker build error: Docker build failed: base image not found, Error: pull access denied for node:18-alpine', 'This error suggests either rate limiting or incorrect image reference. Try using the official node:18 image instead...', 'Claude-3', 1.892, TRUE),
(5, 'Gradle build is timing out after 30 minutes. How can I optimize build performance?', 'To optimize Gradle build performance, enable the daemon, use parallel execution, and increase available memory...', 'GPT-4', 3.121, TRUE),
(8, 'Kubernetes deployment exceeding progress deadline. What could be the issue?', 'Deployment timeouts in Kubernetes can be caused by resource constraints, slow image pulls, or insufficient replicas...', 'Claude-3', 2.678, TRUE),
(3, 'Maven compilation failing with package does not exist error', 'This Maven compilation error indicates missing dependencies or incorrect package structure...', 'GPT-4', 1.456, TRUE),
(6, 'Rails database migration error: ActiveRecord::PendingMigrationError', 'This Rails error occurs when there are pending database migrations that need to be run...', 'Claude-3', 1.789, TRUE),
(4, 'Python ImportError: No module named pytest during GitHub Actions', 'This Python import error indicates that pytest is not installed in the current environment...', 'GPT-4', 1.234, TRUE),
(9, 'JUnit tests failing in UserServiceTest.java with 5 test failures', 'Unit test failures typically indicate changes in business logic or test assertions that need updating...', 'Claude-3', 2.001, TRUE),
(10, 'Terraform plan failed with invalid provider configuration error', 'This Terraform error suggests issues with provider configuration or version compatibility...', 'GPT-4', 1.567, TRUE),
(7, 'ESLint found 15 errors in TypeScript React components', 'ESLint errors in TypeScript React code can be resolved by fixing style violations or updating configuration...', 'Claude-3', 1.345, TRUE);

-- Sample RESOLUTION_METRICS data
INSERT INTO RESOLUTION_METRICS (error_id, solution_id, resolution_status, lookup_time, llm_time, total_time, user_feedback) VALUES
(1, 1, 'Found', 0.125, 0.000, 0.125, 'Success'),
(2, 2, 'Generated', 0.089, 1.892, 1.981, 'Success'),
(3, 3, 'Found', 0.156, 0.000, 0.156, 'Success'),
(4, 4, 'Found', 0.098, 0.000, 0.098, 'Success'),
(5, 5, 'Generated', 0.112, 3.121, 3.233, 'Partial Success'),
(6, 6, 'Found', 0.143, 0.000, 0.143, 'Success'),
(7, 7, 'Found', 0.087, 0.000, 0.087, 'Success'),
(8, 8, 'Generated', 0.134, 2.678, 2.812, 'Needs Review'),
(9, 9, 'Found', 0.167, 0.000, 0.167, 'Success'),
(10, 10, 'Found', 0.121, 0.000, 0.121, 'Success');

-- Sample SOLUTION_FEEDBACK data
INSERT INTO SOLUTION_FEEDBACK (solution_id, feedback_type, comments, user_identifier) VALUES
(1, 'Helpful', 'Solved the npm issue immediately. Great solution!', 'dev.john.smith'),
(2, 'Helpful', 'Docker build works now. Thanks for the image suggestion.', 'devops.sarah.jones'),
(3, 'Needs Update', 'Solution worked but could include more details about Maven scope.', 'backend.mike.wilson'),
(4, 'Helpful', 'Perfect! Added pytest to requirements and tests pass.', 'qa.emma.davis'),
(5, 'Not Helpful', 'Build still times out even with these optimizations.', 'build.alex.brown'),
(6, 'Helpful', 'Migration ran successfully after following these steps.', 'fullstack.lisa.taylor'),
(7, 'Helpful', 'ESLint --fix resolved most issues automatically.', 'frontend.david.clark'),
(8, 'Needs Update', 'Timeout increased but deployment still slow. Need more investigation.', 'sre.rachel.white'),
(9, 'Helpful', 'Updated test assertions and all tests pass now.', 'backend.chris.martin'),
(10, 'Helpful', 'Terraform validate caught the configuration issue.', 'infrastructure.tom.anderson');

-- ================================================
-- ADDITIONAL 30 JENKINS & HARNESS ISSUES
-- ================================================

-- Additional ERRORS (30 Jenkins and Harness specific issues)
INSERT INTO ERRORS (error_fingerprint, source_platform, error_type, error_message, stack_trace, error_context, classification, confidence_score) VALUES
-- Jenkins Issues (20 entries)
('jenkins_001_disk', 'Jenkins', 'Build', 'No space left on device', 'java.io.IOException: No space left on device at java.io.UnixFileSystem.createFileExclusively', 'Build workspace cleanup failure', 'Disk Space', 0.96),
('jenkins_002_agent', 'Jenkins', 'Pipeline', 'Agent is offline', 'Agent computer is offline. Cannot execute build on this agent.', 'Build agent connectivity issue', 'Agent Management', 0.94),
('jenkins_003_perm', 'Jenkins', 'Build', 'Permission denied: jenkins user cannot write', 'java.nio.file.AccessDeniedException: /var/jenkins_home/workspace/project', 'File system permissions issue', 'File Permissions', 0.92),
('jenkins_004_mem', 'Jenkins', 'Pipeline', 'OutOfMemoryError: Java heap space', 'java.lang.OutOfMemoryError: Java heap space at java.util.Arrays.copyOf', 'JVM memory allocation failure', 'Memory Management', 0.95),
('jenkins_005_git', 'Jenkins', 'Build', 'Git clone failed: Authentication failed', 'Cloning into workspace... fatal: Authentication failed for git repository', 'Git credential configuration', 'Git Authentication', 0.93),
('jenkins_006_plugin', 'Jenkins', 'System', 'Plugin dependency not met', 'Failed to load plugin: workflow-step-api v2.24 or later required', 'Plugin version compatibility', 'Plugin Management', 0.89),
('jenkins_007_port', 'Jenkins', 'Pipeline', 'Port 8080 already in use', 'java.net.BindException: Address already in use: bind on port 8080', 'Port conflict during testing', 'Port Management', 0.91),
('jenkins_008_ssl', 'Jenkins', 'Build', 'SSL certificate verification failed', 'javax.net.ssl.SSLHandshakeException: PKIX path building failed', 'SSL/TLS certificate validation', 'SSL Configuration', 0.88),
('jenkins_009_script', 'Jenkins', 'Pipeline', 'Script approval required', 'org.jenkinsci.plugins.scriptsecurity.scripts.UnapprovedUsageException', 'Groovy script security restriction', 'Script Security', 0.90),
('jenkins_010_workspace', 'Jenkins', 'Build', 'Workspace locked by another build', 'Workspace is locked by build #123, waiting for release', 'Concurrent build workspace conflict', 'Workspace Management', 0.87),
('jenkins_011_node', 'Jenkins', 'Pipeline', 'Node.js version not found', 'nodejs: command not found in PATH', 'NodeJS tool configuration missing', 'Tool Configuration', 0.92),
('jenkins_012_maven', 'Jenkins', 'Build', 'Maven JAVA_HOME not set', 'Error: JAVA_HOME is not defined correctly', 'Java environment configuration', 'Environment Configuration', 0.94),
('jenkins_013_docker', 'Jenkins', 'Pipeline', 'Docker daemon not accessible', 'Cannot connect to the Docker daemon at unix:///var/run/docker.sock', 'Docker service connectivity', 'Docker Integration', 0.93),
('jenkins_014_cred', 'Jenkins', 'Build', 'Credentials not found', 'com.cloudbees.plugins.credentials.CredentialsUnavailableException', 'Missing or expired credentials', 'Credential Management', 0.91),
('jenkins_015_queue', 'Jenkins', 'System', 'Build stuck in queue', 'Waiting for next available executor', 'Executor availability or labels', 'Build Queue', 0.85),
('jenkins_016_scm', 'Jenkins', 'Pipeline', 'SCM checkout timeout', 'Timeout after 10 minutes waiting for SCM checkout', 'Source control operation timeout', 'SCM Operations', 0.89),
('jenkins_017_archiva', 'Jenkins', 'Build', 'Artifact archival failed', 'Failed to archive artifacts: Pattern not found', 'Post-build artifact collection', 'Artifact Management', 0.86),
('jenkins_018_email', 'Jenkins', 'Pipeline', 'Email notification failed', 'javax.mail.MessagingException: Could not connect to SMTP host', 'Email service configuration', 'Notification Configuration', 0.88),
('jenkins_019_shared', 'Jenkins', 'Pipeline', 'Shared library load failed', '@Library loading failed: Could not resolve shared-pipeline-library', 'Shared pipeline library configuration', 'Shared Libraries', 0.90),
('jenkins_020_lock', 'Jenkins', 'Build', 'Build resource locked', 'Resource [database] is locked by job project-deploy #456', 'Resource locking conflict', 'Resource Management', 0.84),

-- Harness Issues (10 entries)
('harness_001_conn', 'Harness', 'Pipeline', 'Connector authentication failed', 'Unable to authenticate with Kubernetes cluster using provided credentials', 'K8s connector configuration issue', 'Connector Authentication', 0.95),
('harness_002_deleg', 'Harness', 'Deployment', 'Delegate not available', 'No eligible delegates found for task execution in target environment', 'Delegate connectivity or configuration', 'Delegate Management', 0.93),
('harness_003_secret', 'Harness', 'Pipeline', 'Secret resolution failed', 'Unable to resolve secret: account.aws_access_key from secret manager', 'Secret manager integration issue', 'Secret Management', 0.92),
('harness_004_helm', 'Harness', 'Deployment', 'Helm chart deployment failed', 'Helm install failed: release "myapp" failed with error: timed out waiting for condition', 'Helm deployment timeout or config', 'Helm Deployment', 0.89),
('harness_005_terraform', 'Harness', 'Pipeline', 'Terraform state lock timeout', 'Error acquiring the state lock: ConditionalCheckFailedException', 'Terraform state management conflict', 'Terraform State', 0.91),
('harness_006_approval', 'Harness', 'Pipeline', 'Manual approval timeout', 'Pipeline execution paused: Manual approval step timed out after 24 hours', 'Approval workflow timeout', 'Approval Management', 0.87),
('harness_007_artifact', 'Harness', 'Deployment', 'Artifact download failed', 'Failed to download artifact from Docker registry: connection timeout', 'Artifact registry connectivity', 'Artifact Registry', 0.90),
('harness_008_policy', 'Harness', 'Pipeline', 'Policy evaluation failed', 'OPA policy evaluation failed: deployment violates security policy', 'Open Policy Agent rule violation', 'Policy Governance', 0.94),
('harness_009_infra', 'Harness', 'Deployment', 'Infrastructure provisioning timeout', 'CloudFormation stack creation timed out after 30 minutes', 'Infrastructure provisioning delay', 'Infrastructure Provisioning', 0.88),
('harness_010_rollback', 'Harness', 'Deployment', 'Rollback failed', 'Failed to rollback deployment: previous version not found in registry', 'Deployment rollback failure', 'Rollback Management', 0.86);

-- Additional SOLUTIONS for the new errors
INSERT INTO SOLUTIONS (error_id, solution_steps, code_examples, explanation, solution_source, success_rate, usage_count, is_validated) VALUES
-- Jenkins Solutions
(11, '1. Clean workspace before build\n2. Set up disk monitoring\n3. Configure workspace cleanup policy', 'cleanWs()\n# Add to Jenkins global config:\nWorkspace Cleanup Plugin settings', 'Regular workspace cleanup prevents disk space exhaustion', 'Database', 89.3, 45, TRUE),
(12, '1. Check agent connectivity\n2. Restart Jenkins agent\n3. Verify network and firewall', 'sudo systemctl restart jenkins-agent\n# Check agent logs:\ntail -f /var/log/jenkins/agent.log', 'Agent connectivity issues require service restart and network verification', 'Database', 92.1, 38, TRUE),
(13, '1. Fix file permissions\n2. Update Jenkins user ownership\n3. Configure proper umask', 'sudo chown -R jenkins:jenkins /var/jenkins_home\nsudo chmod -R 755 /var/jenkins_home/workspace', 'File permission issues resolved by proper ownership and permissions', 'Database', 87.8, 52, TRUE),
(14, '1. Increase JVM heap size\n2. Add memory optimization flags\n3. Monitor memory usage', 'JAVA_OPTS="-Xms2g -Xmx4g -XX:+UseG1GC"\n# In jenkins.xml or systemd service', 'Memory issues require JVM tuning and garbage collection optimization', 'LLM', 78.5, 29, TRUE),
(15, '1. Update Git credentials\n2. Configure SSH keys\n3. Test repository access', 'git config --global credential.helper store\n# Or use SSH keys:\nssh-keygen -t rsa -b 4096', 'Git authentication requires proper credential configuration', 'Database', 94.2, 67, TRUE),
(16, '1. Update plugin dependencies\n2. Check compatibility matrix\n3. Restart Jenkins', 'Navigate to Manage Jenkins > Plugin Manager\nUpdate workflow-step-api plugin', 'Plugin compatibility issues require dependency updates', 'Manual', 91.7, 23, TRUE),
(17, '1. Change application port\n2. Kill process using port\n3. Use random port allocation', 'lsof -ti:8080 | xargs kill -9\n# Or use different port:\n-Dserver.port=8081', 'Port conflicts resolved by changing ports or killing conflicting processes', 'Database', 85.9, 34, TRUE),
(18, '1. Update SSL certificates\n2. Configure trust store\n3. Disable SSL verification for internal', 'keytool -import -alias myserver -file server.crt -keystore $JAVA_HOME/lib/security/cacerts', 'SSL issues require certificate management and trust store updates', 'LLM', 73.4, 18, FALSE),
(19, '1. Approve script in Jenkins\n2. Configure script security\n3. Use approved script patterns', 'Manage Jenkins > In-process Script Approval\nApprove pending scripts', 'Script security requires manual approval or security configuration', 'Manual', 96.8, 41, TRUE),
(20, '1. Wait for workspace release\n2. Kill blocking build\n3. Configure concurrent builds', 'Kill blocking build or wait\n# Enable concurrent builds in job config', 'Workspace conflicts require build coordination or concurrent configuration', 'Database', 82.6, 27, TRUE),
(21, '1. Install NodeJS plugin\n2. Configure NodeJS tool\n3. Add to PATH', 'Manage Jenkins > Global Tool Configuration\nAdd NodeJS installation', 'NodeJS configuration requires proper tool setup in Jenkins', 'Database', 90.4, 35, TRUE),
(22, '1. Set JAVA_HOME environment\n2. Configure Maven tool\n3. Verify Java installation', 'export JAVA_HOME=/usr/lib/jvm/java-11-openjdk\n# In Jenkins job or global config', 'Java environment requires proper JAVA_HOME configuration', 'Database', 93.7, 48, TRUE),
(23, '1. Start Docker service\n2. Add Jenkins user to docker group\n3. Configure Docker socket', 'sudo systemctl start docker\nsudo usermod -a -G docker jenkins', 'Docker integration requires service setup and user permissions', 'Database', 88.9, 42, TRUE),
(24, '1. Add missing credentials\n2. Update credential ID\n3. Check credential scope', 'Manage Jenkins > Credentials\nAdd new credential with correct ID', 'Missing credentials require proper credential store configuration', 'Manual', 95.3, 56, TRUE),
(25, '1. Add more executors\n2. Check node labels\n3. Review queue strategy', 'Manage Jenkins > Nodes > Configure\nIncrease number of executors', 'Build queue issues require executor management and label configuration', 'Database', 79.8, 31, TRUE),
(26, '1. Increase SCM timeout\n2. Check network connectivity\n3. Use lightweight checkout', 'checkout([$class: "GitSCM", timeout: 20])\n# Increase timeout in minutes', 'SCM timeout requires network optimization and timeout configuration', 'LLM', 76.2, 22, FALSE),
(27, '1. Fix artifact patterns\n2. Check file paths\n3. Verify post-build actions', 'archiveArtifacts artifacts: "target/*.jar, dist/**", fingerprint: true', 'Artifact archival requires correct path patterns and file verification', 'Database', 84.7, 39, TRUE),
(28, '1. Configure SMTP settings\n2. Test email connection\n3. Check firewall rules', 'Manage Jenkins > Configure System\nE-mail notification settings', 'Email notification requires SMTP configuration and network access', 'Manual', 87.1, 26, TRUE),
(29, '1. Check library repository\n2. Update library version\n3. Configure library path', '@Library("shared-pipeline-library@v1.0") _\n# Check library configuration', 'Shared library issues require repository access and version management', 'Database', 81.5, 33, TRUE),
(30, '1. Release locked resource\n2. Configure resource timeout\n3. Check resource usage', 'Release resource manually or wait for timeout\n# Configure in Lockable Resources plugin', 'Resource locking requires manual intervention or timeout configuration', 'Manual', 77.9, 19, TRUE),

-- Harness Solutions
(31, '1. Update connector credentials\n2. Test connectivity\n3. Verify RBAC permissions', 'kubectl auth can-i create deployments --namespace=default\n# Test cluster access', 'Connector authentication requires credential updates and permission verification', 'Database', 91.6, 28, TRUE),
(32, '1. Restart Harness delegate\n2. Check delegate connectivity\n3. Update delegate version', 'kubectl rollout restart deployment/harness-delegate\n# Check delegate logs', 'Delegate issues require service restart and connectivity verification', 'Database', 89.4, 35, TRUE),
(33, '1. Verify secret manager connection\n2. Check secret path\n3. Update secret permissions', 'Verify secret exists in AWS Secrets Manager\n# Check IAM permissions for secret access', 'Secret resolution requires secret manager configuration and permissions', 'LLM', 86.7, 24, TRUE),
(34, '1. Increase Helm timeout\n2. Check resource limits\n3. Verify chart values', 'helm install myapp ./chart --timeout=10m\n# Check Kubernetes resource availability', 'Helm deployment timeouts require resource optimization and timeout adjustments', 'Database', 83.2, 31, TRUE),
(35, '1. Release Terraform state lock\n2. Check DynamoDB table\n3. Force unlock if needed', 'terraform force-unlock <lock-id>\n# Check state backend configuration', 'Terraform state locks require manual intervention or backend cleanup', 'Manual', 78.8, 17, FALSE),
(36, '1. Extend approval timeout\n2. Configure approval notifications\n3. Set up approval groups', 'Configure approval step timeout in pipeline\n# Set up Slack/email notifications', 'Approval timeouts require workflow configuration and notification setup', 'Database', 92.3, 22, TRUE),
(37, '1. Check registry connectivity\n2. Verify artifact path\n3. Configure retry logic', 'docker pull <registry>/<image>:<tag>\n# Test registry connectivity', 'Artifact download issues require registry configuration and network verification', 'LLM', 88.1, 26, TRUE),
(38, '1. Review OPA policy rules\n2. Update deployment config\n3. Request policy exception', 'Review policy in Harness Policy Management\n# Update deployment to comply with policy', 'Policy violations require configuration updates or policy modifications', 'Manual', 94.7, 19, TRUE),
(39, '1. Check CloudFormation logs\n2. Increase timeout values\n3. Verify resource limits', 'aws cloudformation describe-stack-events --stack-name mystack\n# Check AWS service limits', 'Infrastructure timeouts require resource optimization and limit verification', 'LLM', 81.9, 21, FALSE),
(40, '1. Check artifact availability\n2. Configure rollback strategy\n3. Maintain artifact history', 'Verify previous deployment artifacts exist\n# Configure artifact retention policy', 'Rollback failures require artifact management and strategy configuration', 'Database', 85.6, 15, TRUE);

-- ================================================
-- INDEXES FOR PERFORMANCE
-- ================================================

CREATE INDEX idx_errors_fingerprint ON ERRORS(error_fingerprint);
CREATE INDEX idx_errors_platform ON ERRORS(source_platform);
CREATE INDEX idx_errors_created ON ERRORS(created_at);
CREATE INDEX idx_solutions_error_id ON SOLUTIONS(error_id);
CREATE INDEX idx_solutions_success_rate ON SOLUTIONS(success_rate);
CREATE INDEX idx_pattern_links_error ON ERROR_PATTERN_LINKS(error_id);
CREATE INDEX idx_pattern_links_similarity ON ERROR_PATTERN_LINKS(similarity_score);
CREATE INDEX idx_metrics_timestamp ON RESOLUTION_METRICS(resolution_timestamp);
CREATE INDEX idx_build_contexts_job ON BUILD_CONTEXTS(job_name);

-- ================================================
-- VIEWS FOR COMMON QUERIES
-- ================================================

-- View for error summary with solution status
CREATE VIEW error_summary AS
SELECT 
    e.error_id,
    e.error_fingerprint,
    e.source_platform,
    e.error_type,
    e.classification,
    CASE WHEN s.solution_id IS NOT NULL THEN 'Has Solution' ELSE 'No Solution' END as solution_status,
    s.success_rate,
    e.created_at
FROM ERRORS e
LEFT JOIN SOLUTIONS s ON e.error_id = s.error_id;

-- View for top performing solutions
CREATE VIEW top_solutions AS
SELECT 
    s.solution_id,
    s.solution_source,
    s.success_rate,
    s.usage_count,
    e.error_type,
    e.classification
FROM SOLUTIONS s
JOIN ERRORS e ON s.error_id = e.error_id
WHERE s.success_rate > 80.0
ORDER BY s.success_rate DESC, s.usage_count DESC;






-- ================================================
-- CI/CD Error Resolution System Database Structure
-- ================================================

-- Core Error Storage Table
CREATE TABLE ERRORS (
    error_id SERIAL PRIMARY KEY,
    error_fingerprint VARCHAR(64) UNIQUE NOT NULL,
    source_platform VARCHAR(50) NOT NULL,
    error_type VARCHAR(50) NOT NULL,
    error_message TEXT NOT NULL,
    stack_trace TEXT,
    error_context TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    classification VARCHAR(100),
    confidence_score DECIMAL(3,2)
);

-- Solution Storage Table
CREATE TABLE SOLUTIONS (
    solution_id SERIAL PRIMARY KEY,
    error_id INTEGER REFERENCES ERRORS(error_id),
    solution_steps TEXT NOT NULL,
    code_examples TEXT,
    explanation TEXT,
    solution_source VARCHAR(20) NOT NULL CHECK (solution_source IN ('Database', 'LLM', 'Manual')),
    success_rate DECIMAL(5,2) DEFAULT 0.0,
    usage_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_used_at TIMESTAMP,
    is_validated BOOLEAN DEFAULT FALSE
);

-- Error Pattern Matching Table
CREATE TABLE ERROR_PATTERNS (
    pattern_id SERIAL PRIMARY KEY,
    pattern_hash VARCHAR(64) UNIQUE NOT NULL,
    pattern_template TEXT NOT NULL,
    keywords TEXT,
    metadata TEXT,
    match_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Many-to-Many Link Between Errors and Patterns
CREATE TABLE ERROR_PATTERN_LINKS (
    link_id SERIAL PRIMARY KEY,
    error_id INTEGER REFERENCES ERRORS(error_id),
    pattern_id INTEGER REFERENCES ERROR_PATTERNS(pattern_id),
    similarity_score DECIMAL(3,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Build Context Information
CREATE TABLE BUILD_CONTEXTS (
    context_id SERIAL PRIMARY KEY,
    error_id INTEGER REFERENCES ERRORS(error_id),
    job_name VARCHAR(200) NOT NULL,
    branch_name VARCHAR(100),
    commit_hash VARCHAR(40),
    build_logs TEXT,
    environment VARCHAR(20) NOT NULL CHECK (environment IN ('dev', 'staging', 'prod')),
    build_metadata JSON,
    build_timestamp TIMESTAMP NOT NULL
);

-- LLM Interaction Logging
CREATE TABLE LLM_INTERACTIONS (
    interaction_id SERIAL PRIMARY KEY,
    error_id INTEGER REFERENCES ERRORS(error_id),
    prompt_sent TEXT NOT NULL,
    llm_response TEXT,
    llm_model VARCHAR(50) NOT NULL,
    processing_time DECIMAL(6,3),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    was_successful BOOLEAN DEFAULT FALSE
);

-- Resolution Metrics and Analytics
CREATE TABLE RESOLUTION_METRICS (
    metric_id SERIAL PRIMARY KEY,
    error_id INTEGER REFERENCES ERRORS(error_id),
    solution_id INTEGER REFERENCES SOLUTIONS(solution_id),
    resolution_status VARCHAR(20) NOT NULL CHECK (resolution_status IN ('Found', 'Generated', 'Failed')),
    lookup_time DECIMAL(6,3),
    llm_time DECIMAL(6,3),
    total_time DECIMAL(6,3),
    resolution_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    user_feedback VARCHAR(50)
);

-- User Feedback on Solutions
CREATE TABLE SOLUTION_FEEDBACK (
    feedback_id SERIAL PRIMARY KEY,
    solution_id INTEGER REFERENCES SOLUTIONS(solution_id),
    feedback_type VARCHAR(20) NOT NULL CHECK (feedback_type IN ('Helpful', 'Not Helpful', 'Needs Update')),
    comments TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    user_identifier VARCHAR(100)
);

-- ================================================
-- SAMPLE DATA INSERTION
-- ================================================

-- Sample ERRORS data
INSERT INTO ERRORS (error_fingerprint, source_platform, error_type, error_message, stack_trace, error_context, classification, confidence_score) VALUES
('a1b2c3d4e5f6', 'Jenkins', 'Build', 'npm install failed with exit code 1', 'Error: Cannot resolve dependency @angular/core@^15.0.0', 'Node.js project build step', 'Dependency Resolution', 0.95),
('f6e5d4c3b2a1', 'GitHub Actions', 'Workflow', 'Docker build failed: base image not found', 'Error: pull access denied for node:18-alpine', 'Docker containerization step', 'Docker Configuration', 0.92),
('1a2b3c4d5e6f', 'Jenkins', 'Pipeline', 'Maven compilation error', 'compilation failure: package com.example does not exist', 'Java project compilation', 'Java Compilation', 0.89),
('6f5e4d3c2b1a', 'GitHub Actions', 'Action', 'Python tests failed with ImportError', 'ImportError: No module named pytest', 'Python test execution', 'Python Dependencies', 0.91),
('b1c2d3e4f5g6', 'Jenkins', 'Build', 'Gradle build timeout', 'Build timed out after 30 minutes', 'Long-running Gradle task', 'Build Performance', 0.87),
('g6f5e4d3c2b1', 'GitHub Actions', 'Workflow', 'Database migration failed', 'ActiveRecord::PendingMigrationError', 'Rails application deployment', 'Database Migration', 0.94),
('c1d2e3f4g5h6', 'Jenkins', 'Pipeline', 'ESLint validation failed', 'ESLint found 15 errors in src/components/', 'Code quality check', 'Code Quality', 0.88),
('h6g5f4e3d2c1', 'GitHub Actions', 'Action', 'Kubernetes deployment timeout', 'deployment "api-service" exceeded progress deadline', 'K8s deployment step', 'Kubernetes Deployment', 0.93),
('d1e2f3g4h5i6', 'Jenkins', 'Build', 'Unit tests failed', '5 tests failed in UserServiceTest.java', 'JUnit test execution', 'Unit Testing', 0.90),
('i6h5g4f3e2d1', 'GitHub Actions', 'Workflow', 'Terraform plan failed', 'Error: Invalid provider configuration', 'Infrastructure provisioning', 'Infrastructure as Code', 0.86);

-- Sample SOLUTIONS data
INSERT INTO SOLUTIONS (error_id, solution_steps, code_examples, explanation, solution_source, success_rate, usage_count, is_validated) VALUES
(1, '1. Delete node_modules and package-lock.json\n2. Run npm cache clean --force\n3. Run npm install', 'rm -rf node_modules package-lock.json\nnpm cache clean --force\nnpm install', 'Common npm dependency resolution issue caused by corrupted cache', 'Database', 85.5, 12, TRUE),
(2, '1. Update Dockerfile base image\n2. Use official Node.js image\n3. Rebuild container', 'FROM node:18-alpine\n# Change to:\nFROM node:18', 'Docker Hub rate limiting or incorrect image tag', 'LLM', 92.0, 8, TRUE),
(3, '1. Check import statements\n2. Verify package structure\n3. Update Maven dependencies', '<dependency>\n  <groupId>com.example</groupId>\n  <artifactId>core</artifactId>\n</dependency>', 'Missing Maven dependency in pom.xml', 'Database', 78.3, 15, TRUE),
(4, '1. Install pytest in requirements.txt\n2. Update GitHub Actions workflow\n3. Activate virtual environment', 'pip install pytest\n# Add to requirements.txt:\npytest>=7.0.0', 'Missing test dependency in Python project', 'Database', 91.2, 22, TRUE),
(5, '1. Increase build timeout\n2. Enable Gradle daemon\n3. Add parallel execution', 'timeout: 60m\n# In gradle.properties:\norg.gradle.daemon=true\norg.gradle.parallel=true', 'Gradle build performance optimization needed', 'LLM', 73.8, 6, FALSE),
(6, '1. Run pending migrations\n2. Check database connection\n3. Update schema version', 'rails db:migrate\nrails db:migrate:status', 'Database schema out of sync with application code', 'Database', 88.7, 18, TRUE),
(7, '1. Fix ESLint errors\n2. Update ESLint configuration\n3. Use --fix flag for auto-fixes', 'npx eslint src/ --fix\n# Update .eslintrc.js with proper rules', 'Code style violations preventing build', 'Manual', 95.1, 31, TRUE),
(8, '1. Increase deployment timeout\n2. Check resource limits\n3. Verify image availability', 'kubectl patch deployment api-service -p \'{"spec":{"progressDeadlineSeconds":900}}\'', 'Kubernetes deployment taking longer than expected', 'LLM', 67.4, 4, FALSE),
(9, '1. Review failing test cases\n2. Update test assertions\n3. Mock external dependencies', '@Test\npublic void testUserCreation() {\n  // Updated test logic\n}', 'Unit tests failing due to changed business logic', 'Manual', 82.9, 19, TRUE),
(10, '1. Validate Terraform syntax\n2. Check provider versions\n3. Initialize Terraform', 'terraform init\nterraform validate\nterraform plan', 'Terraform configuration syntax or provider issues', 'Database', 76.5, 11, TRUE);

-- Sample ERROR_PATTERNS data
INSERT INTO ERROR_PATTERNS (pattern_hash, pattern_template, keywords, metadata, match_count) VALUES
('npm_install_fail_001', 'npm install failed with exit code {code}', 'npm, install, exit code, dependency', 'Node.js build failures related to package installation', 25),
('docker_build_fail_001', 'Docker build failed: {image} not found', 'docker, build, image, not found', 'Docker image pull or build failures', 18),
('maven_compile_fail_001', 'Maven compilation error: package {package} does not exist', 'maven, compilation, package, does not exist', 'Java Maven compilation issues', 32),
('python_import_fail_001', 'ImportError: No module named {module}', 'python, import, module, not found', 'Python dependency and import errors', 28),
('gradle_timeout_001', 'Gradle build timeout after {time}', 'gradle, timeout, build, performance', 'Gradle build performance issues', 12),
('rails_migration_fail_001', 'ActiveRecord::PendingMigrationError', 'rails, migration, database, pending', 'Ruby on Rails database migration issues', 22),
('eslint_fail_001', 'ESLint found {count} errors in {path}', 'eslint, errors, code quality, validation', 'JavaScript/TypeScript code quality issues', 45),
('k8s_deploy_fail_001', 'deployment "{name}" exceeded progress deadline', 'kubernetes, deployment, timeout, deadline', 'Kubernetes deployment timeout issues', 15),
('junit_test_fail_001', '{count} tests failed in {file}', 'junit, tests, failed, unit testing', 'Java unit test failures', 38),
('terraform_fail_001', 'Terraform {operation} failed: {reason}', 'terraform, infrastructure, configuration, error', 'Infrastructure as Code issues', 19);

-- Sample ERROR_PATTERN_LINKS data
INSERT INTO ERROR_PATTERN_LINKS (error_id, pattern_id, similarity_score) VALUES
(1, 1, 0.98),
(2, 2, 0.95),
(3, 3, 0.92),
(4, 4, 0.96),
(5, 5, 0.89),
(6, 6, 0.94),
(7, 7, 0.91),
(8, 8, 0.87),
(9, 9, 0.93),
(10, 10, 0.88);

-- Sample BUILD_CONTEXTS data
INSERT INTO BUILD_CONTEXTS (error_id, job_name, branch_name, commit_hash, build_logs, environment, build_metadata, build_timestamp) VALUES
(1, 'frontend-build-pipeline', 'feature/user-dashboard', 'a1b2c3d4e5f678901234567890abcdef12345678', 'npm WARN deprecated package@1.0.0\nnpm ERR! peer dep missing', 'dev', '{"node_version": "18.12.0", "npm_version": "8.19.2"}', '2024-07-28 09:15:23'),
(2, 'api-docker-build', 'main', 'b2c3d4e5f6789012345678901abcdef234567890', 'Step 3/8 : FROM node:18-alpine\npull access denied', 'staging', '{"docker_version": "20.10.17", "buildkit": true}', '2024-07-28 10:22:45'),
(3, 'backend-maven-build', 'develop', 'c3d4e5f6789012345678901abcdef34567890123', '[ERROR] Failed to execute goal org.apache.maven.plugins', 'dev', '{"java_version": "11.0.16", "maven_version": "3.8.6"}', '2024-07-28 11:30:12'),
(4, 'python-test-workflow', 'feature/api-tests', 'd4e5f6789012345678901abcdef4567890123456', 'E   ImportError: No module named pytest\nCollected 0 items', 'dev', '{"python_version": "3.9.13", "pip_version": "22.1.2"}', '2024-07-28 12:45:33'),
(5, 'gradle-build-job', 'release/v2.1', 'e5f6789012345678901abcdef567890123456789', '> Task :compileJava\n> Task :processResources\nBUILD FAILED', 'prod', '{"gradle_version": "7.5.1", "jvm_memory": "4g"}', '2024-07-28 13:20:55'),
(6, 'rails-deploy-pipeline', 'hotfix/db-migration', 'f6789012345678901abcdef67890123456789012', 'Migrating to CreateUserProfiles (20240728000001)\nrails aborted!', 'staging', '{"ruby_version": "3.1.2", "rails_version": "7.0.4"}', '2024-07-28 14:10:17'),
(7, 'frontend-quality-check', 'feature/component-refactor', '789012345678901abcdef7890123456789012345', '/src/components/UserProfile.tsx\n  15:1  error  Missing return type', 'dev', '{"eslint_version": "8.22.0", "typescript_version": "4.8.2"}', '2024-07-28 15:05:41'),
(8, 'k8s-deployment-prod', 'main', '89012345678901abcdef890123456789012345678', 'deployment.apps/api-service condition met\nWaiting for rollout to finish', 'prod', '{"kubectl_version": "1.24.3", "cluster": "prod-cluster"}', '2024-07-28 16:35:29'),
(9, 'backend-unit-tests', 'feature/user-service', '9012345678901abcdef90123456789012345678901', 'Tests run: 25, Failures: 5, Errors: 0, Skipped: 0', 'dev', '{"junit_version": "5.8.2", "mockito_version": "4.6.1"}', '2024-07-28 17:18:52'),
(10, 'infrastructure-terraform', 'feature/new-vpc', '012345678901abcdef0123456789012345678901234', 'Error: Invalid provider configuration for provider[registry.terraform.io/hashicorp/aws]', 'staging', '{"terraform_version": "1.2.9", "aws_provider": "4.28.0"}', '2024-07-28 18:42:16');

-- Sample LLM_INTERACTIONS data
INSERT INTO LLM_INTERACTIONS (error_id, prompt_sent, llm_response, llm_model, processing_time, was_successful) VALUES
(1, 'Analyze this npm install error and provide a solution: npm install failed with exit code 1, Error: Cannot resolve dependency @angular/core@^15.0.0', 'This error indicates a dependency resolution conflict. The solution is to clear the npm cache and reinstall dependencies...', 'GPT-4', 2.345, TRUE),
(2, 'Help fix this Docker build error: Docker build failed: base image not found, Error: pull access denied for node:18-alpine', 'This error suggests either rate limiting or incorrect image reference. Try using the official node:18 image instead...', 'Claude-3', 1.892, TRUE),
(5, 'Gradle build is timing out after 30 minutes. How can I optimize build performance?', 'To optimize Gradle build performance, enable the daemon, use parallel execution, and increase available memory...', 'GPT-4', 3.121, TRUE),
(8, 'Kubernetes deployment exceeding progress deadline. What could be the issue?', 'Deployment timeouts in Kubernetes can be caused by resource constraints, slow image pulls, or insufficient replicas...', 'Claude-3', 2.678, TRUE),
(3, 'Maven compilation failing with package does not exist error', 'This Maven compilation error indicates missing dependencies or incorrect package structure...', 'GPT-4', 1.456, TRUE),
(6, 'Rails database migration error: ActiveRecord::PendingMigrationError', 'This Rails error occurs when there are pending database migrations that need to be run...', 'Claude-3', 1.789, TRUE),
(4, 'Python ImportError: No module named pytest during GitHub Actions', 'This Python import error indicates that pytest is not installed in the current environment...', 'GPT-4', 1.234, TRUE),
(9, 'JUnit tests failing in UserServiceTest.java with 5 test failures', 'Unit test failures typically indicate changes in business logic or test assertions that need updating...', 'Claude-3', 2.001, TRUE),
(10, 'Terraform plan failed with invalid provider configuration error', 'This Terraform error suggests issues with provider configuration or version compatibility...', 'GPT-4', 1.567, TRUE),
(7, 'ESLint found 15 errors in TypeScript React components', 'ESLint errors in TypeScript React code can be resolved by fixing style violations or updating configuration...', 'Claude-3', 1.345, TRUE);

-- Sample RESOLUTION_METRICS data
INSERT INTO RESOLUTION_METRICS (error_id, solution_id, resolution_status, lookup_time, llm_time, total_time, user_feedback) VALUES
(1, 1, 'Found', 0.125, 0.000, 0.125, 'Success'),
(2, 2, 'Generated', 0.089, 1.892, 1.981, 'Success'),
(3, 3, 'Found', 0.156, 0.000, 0.156, 'Success'),
(4, 4, 'Found', 0.098, 0.000, 0.098, 'Success'),
(5, 5, 'Generated', 0.112, 3.121, 3.233, 'Partial Success'),
(6, 6, 'Found', 0.143, 0.000, 0.143, 'Success'),
(7, 7, 'Found', 0.087, 0.000, 0.087, 'Success'),
(8, 8, 'Generated', 0.134, 2.678, 2.812, 'Needs Review'),
(9, 9, 'Found', 0.167, 0.000, 0.167, 'Success'),
(10, 10, 'Found', 0.121, 0.000, 0.121, 'Success');

-- Sample SOLUTION_FEEDBACK data
INSERT INTO SOLUTION_FEEDBACK (solution_id, feedback_type, comments, user_identifier) VALUES
(1, 'Helpful', 'Solved the npm issue immediately. Great solution!', 'dev.john.smith'),
(2, 'Helpful', 'Docker build works now. Thanks for the image suggestion.', 'devops.sarah.jones'),
(3, 'Needs Update', 'Solution worked but could include more details about Maven scope.', 'backend.mike.wilson'),
(4, 'Helpful', 'Perfect! Added pytest to requirements and tests pass.', 'qa.emma.davis'),
(5, 'Not Helpful', 'Build still times out even with these optimizations.', 'build.alex.brown'),
(6, 'Helpful', 'Migration ran successfully after following these steps.', 'fullstack.lisa.taylor'),
(7, 'Helpful', 'ESLint --fix resolved most issues automatically.', 'frontend.david.clark'),
(8, 'Needs Update', 'Timeout increased but deployment still slow. Need more investigation.', 'sre.rachel.white'),
(9, 'Helpful', 'Updated test assertions and all tests pass now.', 'backend.chris.martin'),
(10, 'Helpful', 'Terraform validate caught the configuration issue.', 'infrastructure.tom.anderson');

-- ================================================
-- INDEXES FOR PERFORMANCE
-- ================================================

CREATE INDEX idx_errors_fingerprint ON ERRORS(error_fingerprint);
CREATE INDEX idx_errors_platform ON ERRORS(source_platform);
CREATE INDEX idx_errors_created ON ERRORS(created_at);
CREATE INDEX idx_solutions_error_id ON SOLUTIONS(error_id);
CREATE INDEX idx_solutions_success_rate ON SOLUTIONS(success_rate);
CREATE INDEX idx_pattern_links_error ON ERROR_PATTERN_LINKS(error_id);
CREATE INDEX idx_pattern_links_similarity ON ERROR_PATTERN_LINKS(similarity_score);
CREATE INDEX idx_metrics_timestamp ON RESOLUTION_METRICS(resolution_timestamp);
CREATE INDEX idx_build_contexts_job ON BUILD_CONTEXTS(job_name);

-- ================================================
-- VIEWS FOR COMMON QUERIES
-- ================================================

-- View for error summary with solution status
CREATE VIEW error_summary AS
SELECT 
    e.error_id,
    e.error_fingerprint,
    e.source_platform,
    e.error_type,
    e.classification,
    CASE WHEN s.solution_id IS NOT NULL THEN 'Has Solution' ELSE 'No Solution' END as solution_status,
    s.success_rate,
    e.created_at
FROM ERRORS e
LEFT JOIN SOLUTIONS s ON e.error_id = s.error_id;

-- View for top performing solutions
CREATE VIEW top_solutions AS
SELECT 
    s.solution_id,
    s.solution_source,
    s.success_rate,
    s.usage_count,
    e.error_type,
    e.classification
FROM SOLUTIONS s
JOIN ERRORS e ON s.error_id = e.error_id
WHERE s.success_rate > 80.0
ORDER BY s.success_rate DESC, s.usage_count DESC;




erDiagram
    %% Core Error Storage
    ERRORS {
        int error_id PK
        string error_fingerprint UK "Unique error pattern hash"
        string source_platform "Jenkins/GitHub/etc"
        string error_type "Build/Pipeline/Workflow"
        text error_message
        text stack_trace
        text error_context
        datetime created_at
        datetime updated_at
        string classification "Auto-classified category"
        float confidence_score "Pattern matching confidence"
    }

    %% Solution Storage
    SOLUTIONS {
        int solution_id PK
        int error_id FK
        text solution_steps "Step-by-step fix guide"
        text code_examples "Sample code/commands"
        text explanation "LLM generated reasoning"
        string solution_source "Database/LLM/Manual"
        float success_rate "Historical success percentage"
        int usage_count "How many times used"
        datetime created_at
        datetime last_used_at
        boolean is_validated "Quality checked"
    }

    %% Pattern Matching for Similar Errors
    ERROR_PATTERNS {
        int pattern_id PK
        string pattern_hash UK "Normalized pattern identifier"
        text pattern_template "Template for matching"
        string keywords "Searchable terms"
        text metadata "Additional matching criteria"
        int match_count "How often this pattern matched"
        datetime created_at
        datetime updated_at
    }

    %% Link Errors to Patterns (Many-to-Many)
    ERROR_PATTERN_LINKS {
        int link_id PK
        int error_id FK
        int pattern_id FK
        float similarity_score "How well error matches pattern"
        datetime created_at
    }

    %% Build/Pipeline Context
    BUILD_CONTEXTS {
        int context_id PK
        int error_id FK
        string job_name "Jenkins job/GitHub workflow"
        string branch_name
        string commit_hash
        text build_logs "Relevant log excerpts"
        string environment "dev/staging/prod"
        json build_metadata "Additional context data"
        datetime build_timestamp
    }

    %% LLM Interactions Log
    LLM_INTERACTIONS {
        int interaction_id PK
        int error_id FK
        text prompt_sent "What was sent to LLM"
        text llm_response "Raw LLM response"
        string llm_model "GPT-4/Claude/etc"
        float processing_time "Response time in seconds"
        datetime created_at
        boolean was_successful "Did LLM provide valid solution"
    }

    %% Metrics and Analytics
    RESOLUTION_METRICS {
        int metric_id PK
        int error_id FK
        int solution_id FK
        string resolution_status "Found/Generated/Failed"
        float lookup_time "Time to find in database"
        float llm_time "Time for LLM processing"
        float total_time "End-to-end processing time"
        datetime resolution_timestamp
        string user_feedback "Success/Failure feedback"
    }

    %% Simple User Feedback
    SOLUTION_FEEDBACK {
        int feedback_id PK
        int solution_id FK
        string feedback_type "Helpful/Not Helpful/Needs Update"
        text comments "Optional user comments"
        datetime created_at
        string user_identifier "Who provided feedback"
    }

    %% Relationships
    ERRORS ||--o{ SOLUTIONS : "has"
    ERRORS ||--o{ ERROR_PATTERN_LINKS : "matches"
    ERROR_PATTERNS ||--o{ ERROR_PATTERN_LINKS : "contains"
    ERRORS ||--|| BUILD_CONTEXTS : "occurred_in"
    ERRORS ||--o{ LLM_INTERACTIONS : "processed_by"
    ERRORS ||--o{ RESOLUTION_METRICS : "tracked_in"
    SOLUTIONS ||--o{ RESOLUTION_METRICS : "measured_by"
    SOLUTIONS ||--o{ SOLUTION_FEEDBACK : "receives"


# CI Failure Resolution Solution - Presentation Content

## Slide 1: Title
**CI Failure Resolution: Automated Agentic Solution System**

Intelligent agentic approach using LangGraph to detect, analyze, and resolve CI pipeline failures with automated learning and human oversight

Key Stats:
- Analyze Error Logs
- AI Powered Agentic Solutions  
- Continuous Learning Loop

---

## Slide 2: Problem & Approach

### The Challenge
CI pipeline failures cause development delays and require manual intervention to diagnose and fix issues

### Our Agentic Solution Approach
- **Automated Detection:** Monitor CI status continuously with intelligent agents
- **Intelligent Analysis:** Extract and analyze error logs using LangGraph workflows
- **Knowledge Base:** Maintain error-to-solution database with agent coordination
- **AI Enhancement:** LLM-powered agentic solution generation for unknown errors
- **Human-in-the-Loop:** Engineer validation and selection with agent assistance

---

## Slide 3: Solution Workflow (LangGraph Agentic System)

### Phase 1: Detection & Analysis
Agentic workflow: Check CI failure status → Fetch CI logs → Analyze error logs & code changes

### Phase 2: Solution Discovery
Agent coordination: Search error database for known solutions
If not found: LangGraph agents query LLM for new solutions → Update database

### Phase 3: Implementation
Agentic presentation: Display recommended solutions → Engineer chooses → Re-run CI pipeline

### Phase 4: Validation & Learning
Intelligent loop: Check CI success → If failed: Agent-driven loop back → Continuous improvement

---

## Slide 4: Key Benefits & Impact

### Core Advantages
- **Automated Agentic Intelligence:** Self-improving LangGraph system that learns from each failure
- **Engineer Empowerment:** Agentic solutions provide curated options, engineer maintains control
- **Continuous Learning:** Unknown errors become known solutions through agent coordination
- **Reduced Downtime:** Faster identification and resolution via intelligent agent workflows
- **Knowledge Preservation:** Institutional knowledge captured through agentic database management

### Result
Transform CI failures from time-consuming manual tasks into guided, intelligent agentic resolution workflows powered by LangGraph
