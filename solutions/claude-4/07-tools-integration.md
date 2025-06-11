# Tools Integration Guide - Context7 & TaskMaster-AI

## Table of Contents
1. [Context7 Integration](#context7-integration)
2. [TaskMaster-AI Integration](#taskmaster-ai-integration)
3. [Development Workflow Integration](#development-workflow-integration)
4. [Documentation Generation](#documentation-generation)
5. [AI-Assisted Development](#ai-assisted-development)
6. [Best Practices](#best-practices)

## Context7 Integration

### Overview
Context7 is a powerful library documentation and code reference system that provides AI assistants with comprehensive, authoritative documentation from popular libraries and frameworks.

### Installation & Setup

```bash
# Install Context7 CLI (if available)
npm install -g context7-cli

# Or integrate directly with compatible AI tools
# Context7 is typically accessed through AI assistants like Claude
```

### Library Resolution and Documentation Retrieval

#### Resolving Library IDs

```typescript
// lib/context7.ts
interface Context7Library {
  id: string;
  name: string;
  description: string;
  codeSnippets: number;
  trustScore: number;
  versions?: string[];
}

export class Context7Service {
  async resolveLibraryId(libraryName: string): Promise<Context7Library[]> {
    // This would typically be handled by AI assistant integration
    // Example for common NextJS libraries:
    const commonLibraries: Record<string, string> = {
      'next.js': '/vercel/next.js',
      'supabase': '/supabase/supabase',
      'stripe': '/stripe/stripe-node',
      'react-stripe': '/stripe/react-stripe-js',
      'shadcn/ui': '/shadcn-ui/ui',
      'prisma': '/prisma/prisma',
      'tailwindcss': '/tailwindlabs/tailwindcss',
    };

    return [{
      id: commonLibraries[libraryName.toLowerCase()] || '',
      name: libraryName,
      description: `Documentation for ${libraryName}`,
      codeSnippets: 0,
      trustScore: 0,
    }];
  }

  async getLibraryDocs(
    libraryId: string,
    tokens: number = 3000,
    topic?: string
  ): Promise<string> {
    // In practice, this would be handled by the AI assistant
    // Returns formatted documentation with code examples
    return `
      Documentation for ${libraryId}
      Topic: ${topic || 'general'}
      Token limit: ${tokens}
    `;
  }
}
```

#### Integration with Development Workflow

```typescript
// scripts/generate-docs.ts
import { Context7Service } from '@/lib/context7';

export async function generateProjectDocs() {
  const context7 = new Context7Service();
  
  const libraries = [
    'next.js',
    'supabase',
    'stripe',
    'shadcn/ui',
    'tailwindcss'
  ];

  const docs: Record<string, string> = {};

  for (const library of libraries) {
    console.log(`Generating documentation for ${library}...`);
    
    const libraryInfo = await context7.resolveLibraryId(library);
    if (libraryInfo.length > 0) {
      const documentation = await context7.getLibraryDocs(
        libraryInfo[0].id,
        5000,
        'integration setup configuration'
      );
      
      docs[library] = documentation;
    }
  }

  return docs;
}

// Usage in package.json scripts
// "scripts": {
//   "docs:generate": "tsx scripts/generate-docs.ts"
// }
```

### Context7-Enhanced Code Generation

```typescript
// lib/ai-code-generator.ts
interface CodeGenerationRequest {
  component: string;
  framework: string;
  requirements: string[];
  libraries: string[];
}

export class AICodeGenerator {
  private context7: Context7Service;

  constructor() {
    this.context7 = new Context7Service();
  }

  async generateComponent(request: CodeGenerationRequest): Promise<string> {
    // Step 1: Gather library documentation
    const libraryDocs = await Promise.all(
      request.libraries.map(async (lib) => {
        const libInfo = await this.context7.resolveLibraryId(lib);
        if (libInfo.length > 0) {
          return await this.context7.getLibraryDocs(
            libInfo[0].id,
            2000,
            request.component
          );
        }
        return '';
      })
    );

    // Step 2: Create enhanced prompt with Context7 documentation
    const enhancedPrompt = `
      Generate a ${request.component} component for ${request.framework}
      
      Requirements:
      ${request.requirements.map(req => `- ${req}`).join('\n')}
      
      Available Libraries and Documentation:
      ${libraryDocs.filter(doc => doc).join('\n\n')}
      
      Please ensure the component follows best practices and uses the documented APIs correctly.
    `;

    // Step 3: This would integrate with your AI service
    return this.generateWithAI(enhancedPrompt);
  }

  private async generateWithAI(prompt: string): Promise<string> {
    // Integration with AI service (OpenAI, Anthropic, etc.)
    return `// Generated component based on Context7 documentation\n// ${prompt}`;
  }
}
```

### Documentation Validation

```typescript
// lib/doc-validator.ts
export class DocumentationValidator {
  private context7: Context7Service;

  constructor() {
    this.context7 = new Context7Service();
  }

  async validateCodeAgainstDocs(
    code: string,
    libraries: string[]
  ): Promise<ValidationResult> {
    const issues: ValidationIssue[] = [];

    for (const library of libraries) {
      const docs = await this.context7.getLibraryDocs(
        `/library/${library}`,
        3000,
        'api methods patterns'
      );

      // Extract API usage from code
      const apiUsage = this.extractAPIUsage(code, library);
      
      // Validate against documentation
      const libraryIssues = this.validateUsage(apiUsage, docs);
      issues.push(...libraryIssues);
    }

    return {
      isValid: issues.length === 0,
      issues,
      suggestions: this.generateSuggestions(issues),
    };
  }

  private extractAPIUsage(code: string, library: string): APIUsage[] {
    // Extract API calls, imports, and usage patterns
    const importRegex = new RegExp(`import.*from ['"]${library}['"]`, 'g');
    const imports = code.match(importRegex) || [];
    
    return imports.map(imp => ({
      library,
      usage: imp,
      line: this.getLineNumber(code, imp),
    }));
  }

  private validateUsage(usage: APIUsage[], docs: string): ValidationIssue[] {
    // Validate API usage against documentation
    return [];
  }

  private generateSuggestions(issues: ValidationIssue[]): string[] {
    return issues.map(issue => `Fix ${issue.type}: ${issue.suggestion}`);
  }

  private getLineNumber(code: string, text: string): number {
    const lines = code.substring(0, code.indexOf(text)).split('\n');
    return lines.length;
  }
}

interface ValidationResult {
  isValid: boolean;
  issues: ValidationIssue[];
  suggestions: string[];
}

interface ValidationIssue {
  type: 'deprecated' | 'incorrect' | 'missing';
  library: string;
  line: number;
  message: string;
  suggestion: string;
}

interface APIUsage {
  library: string;
  usage: string;
  line: number;
}
```

## TaskMaster-AI Integration

### Overview
TaskMaster-AI provides intelligent task management, code review, and development workflow automation capabilities.

### Configuration

```typescript
// lib/taskmaster.ts
interface TaskMasterConfig {
  apiKey: string;
  projectId: string;
  workspace: string;
  integrations: {
    github: boolean;
    vercel: boolean;
    supabase: boolean;
    stripe: boolean;
  };
}

export class TaskMasterAI {
  private config: TaskMasterConfig;

  constructor(config: TaskMasterConfig) {
    this.config = config;
  }

  async createTask(task: TaskDefinition): Promise<Task> {
    const response = await fetch('/api/taskmaster/tasks', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${this.config.apiKey}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        ...task,
        projectId: this.config.projectId,
      }),
    });

    return await response.json();
  }

  async analyzeCodebase(): Promise<CodebaseAnalysis> {
    const analysis = await fetch('/api/taskmaster/analyze', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${this.config.apiKey}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        projectId: this.config.projectId,
        workspace: this.config.workspace,
      }),
    });

    return await analysis.json();
  }

  async generateTasks(analysis: CodebaseAnalysis): Promise<Task[]> {
    const response = await fetch('/api/taskmaster/generate-tasks', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${this.config.apiKey}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        analysis,
        projectId: this.config.projectId,
      }),
    });

    return await response.json();
  }
}

interface TaskDefinition {
  title: string;
  description: string;
  type: 'feature' | 'bug' | 'refactor' | 'docs' | 'test';
  priority: 'low' | 'medium' | 'high' | 'critical';
  tags: string[];
  assignee?: string;
  estimatedHours?: number;
}

interface Task extends TaskDefinition {
  id: string;
  status: 'todo' | 'in-progress' | 'review' | 'done';
  createdAt: string;
  updatedAt: string;
  dependencies: string[];
}

interface CodebaseAnalysis {
  complexity: number;
  coverage: number;
  debt: TechnicalDebt[];
  patterns: DesignPattern[];
  suggestions: Suggestion[];
}

interface TechnicalDebt {
  file: string;
  line: number;
  type: 'code-smell' | 'duplication' | 'complexity' | 'performance';
  severity: 'low' | 'medium' | 'high';
  description: string;
  estimatedEffort: number;
}

interface DesignPattern {
  name: string;
  files: string[];
  score: number;
  suggestions: string[];
}

interface Suggestion {
  type: 'improvement' | 'optimization' | 'security';
  description: string;
  impact: 'low' | 'medium' | 'high';
  effort: 'low' | 'medium' | 'high';
}
```

### Automated Code Review

```typescript
// lib/code-review.ts
export class AICodeReviewer {
  private taskmaster: TaskMasterAI;

  constructor(taskmaster: TaskMasterAI) {
    this.taskmaster = taskmaster;
  }

  async reviewPullRequest(prData: PullRequestData): Promise<CodeReview> {
    const review = await fetch('/api/taskmaster/review', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${this.taskmaster.config.apiKey}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        pullRequest: prData,
        context: await this.gatherContext(prData),
      }),
    });

    return await review.json();
  }

  private async gatherContext(prData: PullRequestData): Promise<ReviewContext> {
    return {
      projectStructure: await this.analyzeProjectStructure(),
      dependencies: await this.analyzeDependencies(),
      testCoverage: await this.getTestCoverage(),
      codebasePatterns: await this.analyzePatterns(),
    };
  }

  async generateReviewSummary(review: CodeReview): Promise<string> {
    const summary = `
# Code Review Summary

## Overall Score: ${review.score}/10

## Key Findings:
${review.findings.map(f => `- **${f.type}**: ${f.description}`).join('\n')}

## Recommendations:
${review.recommendations.map(r => `- ${r.description} (Priority: ${r.priority})`).join('\n')}

## Security Issues:
${review.security.issues.length > 0 
  ? review.security.issues.map(i => `- **${i.severity}**: ${i.description}`).join('\n')
  : 'No security issues found.'}

## Performance Considerations:
${review.performance.suggestions.map(s => `- ${s}`).join('\n')}
    `;

    return summary;
  }

  private async analyzeProjectStructure(): Promise<ProjectStructure> {
    // Analyze current project structure
    return {
      components: [],
      pages: [],
      apis: [],
      utils: [],
    };
  }

  private async analyzeDependencies(): Promise<DependencyAnalysis> {
    // Analyze package.json and dependencies
    return {
      outdated: [],
      vulnerable: [],
      unused: [],
    };
  }

  private async getTestCoverage(): Promise<TestCoverage> {
    // Get test coverage information
    return {
      percentage: 0,
      files: [],
      uncovered: [],
    };
  }

  private async analyzePatterns(): Promise<CodePattern[]> {
    // Analyze code patterns and best practices
    return [];
  }
}

interface PullRequestData {
  id: string;
  title: string;
  description: string;
  files: FileChange[];
  author: string;
  branch: string;
}

interface FileChange {
  path: string;
  status: 'added' | 'modified' | 'deleted';
  additions: number;
  deletions: number;
  content: string;
}

interface CodeReview {
  score: number;
  findings: ReviewFinding[];
  recommendations: Recommendation[];
  security: SecurityAnalysis;
  performance: PerformanceAnalysis;
}

interface ReviewFinding {
  type: 'bug' | 'improvement' | 'style' | 'performance';
  file: string;
  line: number;
  description: string;
  severity: 'low' | 'medium' | 'high';
}

interface Recommendation {
  description: string;
  priority: 'low' | 'medium' | 'high';
  effort: 'low' | 'medium' | 'high';
}

interface SecurityAnalysis {
  score: number;
  issues: SecurityIssue[];
}

interface SecurityIssue {
  type: 'vulnerability' | 'exposure' | 'configuration';
  severity: 'low' | 'medium' | 'high' | 'critical';
  description: string;
  file: string;
  line?: number;
}

interface PerformanceAnalysis {
  score: number;
  suggestions: string[];
}
```

### Task Automation

```typescript
// lib/task-automation.ts
export class TaskAutomation {
  private taskmaster: TaskMasterAI;

  constructor(taskmaster: TaskMasterAI) {
    this.taskmaster = taskmaster;
  }

  async setupAutomation(): Promise<void> {
    await this.createGitHubWorkflows();
    await this.setupVercelIntegration();
    await this.configureSupabaseHooks();
    await this.setupStripeWebhooks();
  }

  private async createGitHubWorkflows(): Promise<void> {
    const workflows = [
      this.createCIWorkflow(),
      this.createCodeReviewWorkflow(),
      this.createDeploymentWorkflow(),
    ];

    for (const workflow of workflows) {
      await this.createWorkflowFile(workflow);
    }
  }

  private createCIWorkflow(): GitHubWorkflow {
    return {
      name: 'CI Pipeline',
      file: '.github/workflows/ci.yml',
      content: `
name: CI Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '18'
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Run linting
      run: npm run lint
    
    - name: Run type checking
      run: npm run type-check
    
    - name: Run tests
      run: npm run test
    
    - name: Build application
      run: npm run build
    
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage/lcov.info
      `,
    };
  }

  private createCodeReviewWorkflow(): GitHubWorkflow {
    return {
      name: 'AI Code Review',
      file: '.github/workflows/code-review.yml',
      content: `
name: AI Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  ai-review:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
      with:
        fetch-depth: 0
    
    - name: AI Code Review
      uses: ./actions/ai-review
      with:
        taskmaster-api-key: \${{ secrets.TASKMASTER_API_KEY }}
        github-token: \${{ secrets.GITHUB_TOKEN }}
      `,
    };
  }

  private createDeploymentWorkflow(): GitHubWorkflow {
    return {
      name: 'Deploy to Vercel',
      file: '.github/workflows/deploy.yml',
      content: `
name: Deploy to Vercel

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Deploy to Vercel
      uses: amondnet/vercel-action@v25
      with:
        vercel-token: \${{ secrets.VERCEL_TOKEN }}
        vercel-org-id: \${{ secrets.VERCEL_ORG_ID }}
        vercel-project-id: \${{ secrets.VERCEL_PROJECT_ID }}
        vercel-args: '--prod'
      `,
    };
  }

  private async createWorkflowFile(workflow: GitHubWorkflow): Promise<void> {
    // Create GitHub workflow file
    console.log(`Creating workflow: ${workflow.name}`);
  }

  private async setupVercelIntegration(): Promise<void> {
    // Setup Vercel deployment integration
    const integration = {
      name: 'TaskMaster-AI Vercel Integration',
      hooks: {
        'deployment.created': '/api/taskmaster/deployment-created',
        'deployment.succeeded': '/api/taskmaster/deployment-succeeded',
        'deployment.failed': '/api/taskmaster/deployment-failed',
      },
    };

    console.log('Setting up Vercel integration:', integration);
  }

  private async configureSupabaseHooks(): Promise<void> {
    // Configure Supabase database hooks
    const hooks = [
      {
        name: 'user_created',
        events: ['INSERT'],
        table: 'users',
        webhook: '/api/taskmaster/user-created',
      },
      {
        name: 'order_updated',
        events: ['UPDATE'],
        table: 'orders',
        webhook: '/api/taskmaster/order-updated',
      },
    ];

    console.log('Configuring Supabase hooks:', hooks);
  }

  private async setupStripeWebhooks(): Promise<void> {
    // Setup Stripe webhook integration
    const webhooks = [
      {
        events: ['payment_intent.succeeded'],
        url: '/api/taskmaster/payment-succeeded',
      },
      {
        events: ['customer.subscription.created'],
        url: '/api/taskmaster/subscription-created',
      },
    ];

    console.log('Setting up Stripe webhooks:', webhooks);
  }
}

interface GitHubWorkflow {
  name: string;
  file: string;
  content: string;
}
```

## Development Workflow Integration

### AI-Powered Development Assistant

```typescript
// lib/dev-assistant.ts
export class DevelopmentAssistant {
  private context7: Context7Service;
  private taskmaster: TaskMasterAI;

  constructor(context7: Context7Service, taskmaster: TaskMasterAI) {
    this.context7 = context7;
    this.taskmaster = taskmaster;
  }

  async assistWithFeature(feature: FeatureRequest): Promise<FeatureImplementation> {
    // Step 1: Analyze requirements
    const analysis = await this.analyzeRequirements(feature);
    
    // Step 2: Generate implementation plan
    const plan = await this.generateImplementationPlan(analysis);
    
    // Step 3: Create tasks
    const tasks = await this.createImplementationTasks(plan);
    
    // Step 4: Generate code scaffolding
    const scaffolding = await this.generateCodeScaffolding(plan);
    
    return {
      analysis,
      plan,
      tasks,
      scaffolding,
    };
  }

  private async analyzeRequirements(feature: FeatureRequest): Promise<RequirementsAnalysis> {
    const libraryDocs = await Promise.all(
      feature.technologies.map(tech => 
        this.context7.getLibraryDocs(`/library/${tech}`, 2000, feature.description)
      )
    );

    return {
      complexity: this.calculateComplexity(feature),
      dependencies: this.identifyDependencies(feature),
      techStack: feature.technologies,
      documentation: libraryDocs,
      estimatedHours: this.estimateEffort(feature),
    };
  }

  private async generateImplementationPlan(analysis: RequirementsAnalysis): Promise<ImplementationPlan> {
    return {
      phases: [
        {
          name: 'Setup & Configuration',
          tasks: ['Environment setup', 'Dependencies installation'],
          estimatedHours: 2,
        },
        {
          name: 'Core Implementation',
          tasks: ['Component development', 'API integration'],
          estimatedHours: analysis.estimatedHours * 0.6,
        },
        {
          name: 'Testing & Documentation',
          tasks: ['Unit tests', 'Integration tests', 'Documentation'],
          estimatedHours: analysis.estimatedHours * 0.3,
        },
        {
          name: 'Deployment & Monitoring',
          tasks: ['Deployment setup', 'Monitoring configuration'],
          estimatedHours: analysis.estimatedHours * 0.1,
        },
      ],
      architecture: this.generateArchitecture(analysis),
      files: this.identifyFilesToCreate(analysis),
    };
  }

  private async createImplementationTasks(plan: ImplementationPlan): Promise<Task[]> {
    const tasks: Task[] = [];

    for (const phase of plan.phases) {
      for (const taskDescription of phase.tasks) {
        const task = await this.taskmaster.createTask({
          title: taskDescription,
          description: `Implementation task for ${phase.name}`,
          type: this.determineTaskType(taskDescription),
          priority: 'medium',
          tags: ['feature', 'implementation'],
          estimatedHours: phase.estimatedHours / phase.tasks.length,
        });
        tasks.push(task);
      }
    }

    return tasks;
  }

  private async generateCodeScaffolding(plan: ImplementationPlan): Promise<CodeScaffolding> {
    const files: ScaffoldFile[] = [];

    for (const file of plan.files) {
      const content = await this.generateFileContent(file, plan);
      files.push({
        path: file.path,
        content: content,
        type: file.type,
      });
    }

    return { files };
  }

  private calculateComplexity(feature: FeatureRequest): number {
    // Calculate feature complexity based on requirements
    return feature.requirements.length * 2 + feature.technologies.length;
  }

  private identifyDependencies(feature: FeatureRequest): string[] {
    // Identify external dependencies needed
    return feature.technologies;
  }

  private estimateEffort(feature: FeatureRequest): number {
    // Estimate development effort in hours
    return this.calculateComplexity(feature) * 2;
  }

  private generateArchitecture(analysis: RequirementsAnalysis): Architecture {
    return {
      components: [],
      services: [],
      database: [],
      apis: [],
    };
  }

  private identifyFilesToCreate(analysis: RequirementsAnalysis): FileToCreate[] {
    return [
      { path: 'components/feature.tsx', type: 'component' },
      { path: 'lib/feature-service.ts', type: 'service' },
      { path: 'app/api/feature/route.ts', type: 'api' },
      { path: '__tests__/feature.test.tsx', type: 'test' },
    ];
  }

  private determineTaskType(description: string): TaskDefinition['type'] {
    if (description.includes('test')) return 'test';
    if (description.includes('doc')) return 'docs';
    if (description.includes('bug')) return 'bug';
    return 'feature';
  }

  private async generateFileContent(file: FileToCreate, plan: ImplementationPlan): Promise<string> {
    // Generate file content based on type and plan
    return `// Generated ${file.type} file: ${file.path}\n// TODO: Implement based on plan`;
  }
}

interface FeatureRequest {
  name: string;
  description: string;
  requirements: string[];
  technologies: string[];
  priority: 'low' | 'medium' | 'high';
}

interface RequirementsAnalysis {
  complexity: number;
  dependencies: string[];
  techStack: string[];
  documentation: string[];
  estimatedHours: number;
}

interface ImplementationPlan {
  phases: Phase[];
  architecture: Architecture;
  files: FileToCreate[];
}

interface Phase {
  name: string;
  tasks: string[];
  estimatedHours: number;
}

interface Architecture {
  components: string[];
  services: string[];
  database: string[];
  apis: string[];
}

interface FileToCreate {
  path: string;
  type: 'component' | 'service' | 'api' | 'test' | 'config';
}

interface FeatureImplementation {
  analysis: RequirementsAnalysis;
  plan: ImplementationPlan;
  tasks: Task[];
  scaffolding: CodeScaffolding;
}

interface CodeScaffolding {
  files: ScaffoldFile[];
}

interface ScaffoldFile {
  path: string;
  content: string;
  type: string;
}
```

## Documentation Generation

### Automated Documentation with Context7

```typescript
// scripts/generate-documentation.ts
import { Context7Service } from '@/lib/context7';
import { TaskMasterAI } from '@/lib/taskmaster';
import fs from 'fs/promises';
import path from 'path';

export class DocumentationGenerator {
  private context7: Context7Service;
  private taskmaster: TaskMasterAI;

  constructor() {
    this.context7 = new Context7Service();
    this.taskmaster = new TaskMasterAI({
      apiKey: process.env.TASKMASTER_API_KEY!,
      projectId: process.env.TASKMASTER_PROJECT_ID!,
      workspace: process.cwd(),
      integrations: {
        github: true,
        vercel: true,
        supabase: true,
        stripe: true,
      },
    });
  }

  async generateComprehensiveDocs(): Promise<void> {
    console.log('Generating comprehensive documentation...');

    // Analyze current codebase
    const analysis = await this.taskmaster.analyzeCodebase();
    
    // Generate documentation for each category
    const docs = await Promise.all([
      this.generateTechStackDocs(),
      this.generateAPIDocumentation(),
      this.generateComponentDocumentation(),
      this.generateDeploymentGuide(),
      this.generateTroubleshootingGuide(),
    ]);

    // Write documentation files
    await this.writeDocumentationFiles(docs);
    
    console.log('Documentation generation complete!');
  }

  private async generateTechStackDocs(): Promise<DocumentationFile> {
    const techStack = [
      'next.js',
      'supabase',
      'stripe',
      'shadcn/ui',
      'tailwindcss',
      'prisma',
    ];

    let content = '# Technology Stack Documentation\n\n';

    for (const tech of techStack) {
      console.log(`Generating docs for ${tech}...`);
      
      const docs = await this.context7.getLibraryDocs(
        `/library/${tech}`,
        3000,
        'installation configuration integration best practices'
      );

      content += `## ${tech}\n\n${docs}\n\n`;
    }

    return {
      filename: 'tech-stack.md',
      content,
      category: 'setup',
    };
  }

  private async generateAPIDocumentation(): Promise<DocumentationFile> {
    const apiRoutes = await this.discoverAPIRoutes();
    
    let content = '# API Documentation\n\n';
    content += 'This document provides comprehensive API documentation for all endpoints.\n\n';

    for (const route of apiRoutes) {
      content += `## ${route.method} ${route.path}\n\n`;
      content += `${route.description}\n\n`;
      
      if (route.parameters.length > 0) {
        content += '### Parameters\n\n';
        route.parameters.forEach(param => {
          content += `- **${param.name}** (${param.type}): ${param.description}\n`;
        });
        content += '\n';
      }

      content += '### Example Request\n\n';
      content += '```javascript\n';
      content += route.exampleRequest;
      content += '\n```\n\n';

      content += '### Example Response\n\n';
      content += '```json\n';
      content += route.exampleResponse;
      content += '\n```\n\n';
    }

    return {
      filename: 'api-documentation.md',
      content,
      category: 'api',
    };
  }

  private async generateComponentDocumentation(): Promise<DocumentationFile> {
    const components = await this.discoverComponents();
    
    let content = '# Component Documentation\n\n';
    content += 'This document provides documentation for all React components.\n\n';

    for (const component of components) {
      content += `## ${component.name}\n\n`;
      content += `${component.description}\n\n`;
      
      content += '### Props\n\n';
      component.props.forEach(prop => {
        content += `- **${prop.name}** (${prop.type}): ${prop.description}\n`;
      });
      content += '\n';

      content += '### Usage Example\n\n';
      content += '```tsx\n';
      content += component.example;
      content += '\n```\n\n';
    }

    return {
      filename: 'components.md',
      content,
      category: 'components',
    };
  }

  private async generateDeploymentGuide(): Promise<DocumentationFile> {
    const content = `
# Deployment Guide

## Prerequisites

Before deploying, ensure you have:

1. ✅ Vercel account and CLI installed
2. ✅ Supabase project configured
3. ✅ Stripe account with API keys
4. ✅ Environment variables configured

## Deployment Steps

### 1. Environment Variables

Set the following environment variables in Vercel:

\`\`\`bash
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
STRIPE_SECRET_KEY=your_stripe_secret_key
STRIPE_WEBHOOK_SECRET=your_webhook_secret
NEXTAUTH_SECRET=your_nextauth_secret
NEXTAUTH_URL=your_production_url
\`\`\`

### 2. Database Migration

Run Supabase migrations:

\`\`\`bash
npx supabase db push
\`\`\`

### 3. Deploy to Vercel

\`\`\`bash
vercel --prod
\`\`\`

### 4. Post-Deployment Tasks

1. ✅ Test authentication flow
2. ✅ Verify payment integration
3. ✅ Check database connections
4. ✅ Monitor application performance

## Monitoring

Set up monitoring for:

- Application performance (Vercel Analytics)
- Error tracking (Sentry)
- Database performance (Supabase)
- Payment processing (Stripe Dashboard)
    `;

    return {
      filename: 'deployment-guide.md',
      content,
      category: 'deployment',
    };
  }

  private async generateTroubleshootingGuide(): Promise<DocumentationFile> {
    const content = `
# Troubleshooting Guide

## Common Issues

### Authentication Issues

**Issue**: Users can't sign in with social providers

**Solution**:
1. Check OAuth app configuration
2. Verify redirect URLs
3. Ensure environment variables are set

### Payment Processing Issues

**Issue**: Stripe payments failing

**Solution**:
1. Verify Stripe webhook endpoints
2. Check API key configuration
3. Review payment flow logs

### Database Connection Issues

**Issue**: Supabase connection timeouts

**Solution**:
1. Check connection string
2. Verify service role key
3. Review RLS policies

### Deployment Issues

**Issue**: Build failures on Vercel

**Solution**:
1. Check environment variables
2. Review build logs
3. Verify dependencies

## Debug Tools

### Logging

Use structured logging for debugging:

\`\`\`typescript
console.log('[DEBUG]', { user: userId, action: 'payment' });
\`\`\`

### Error Boundaries

Implement error boundaries for better error handling:

\`\`\`tsx
<ErrorBoundary fallback={<ErrorPage />}>
  <PaymentForm />
</ErrorBoundary>
\`\`\`
    `;

    return {
      filename: 'troubleshooting.md',
      content,
      category: 'support',
    };
  }

  private async writeDocumentationFiles(docs: DocumentationFile[]): Promise<void> {
    const docsDir = path.join(process.cwd(), 'docs');
    
    // Ensure docs directory exists
    await fs.mkdir(docsDir, { recursive: true });

    for (const doc of docs) {
      const filePath = path.join(docsDir, doc.filename);
      await fs.writeFile(filePath, doc.content, 'utf-8');
      console.log(`✅ Generated ${doc.filename}`);
    }
  }

  private async discoverAPIRoutes(): Promise<APIRoute[]> {
    // Discover API routes from the app directory
    return [
      {
        method: 'POST',
        path: '/api/auth/signin',
        description: 'Authenticate user with email/password',
        parameters: [
          { name: 'email', type: 'string', description: 'User email address' },
          { name: 'password', type: 'string', description: 'User password' },
        ],
        exampleRequest: 'fetch("/api/auth/signin", { method: "POST", body: JSON.stringify({ email, password }) })',
        exampleResponse: '{ "user": { "id": "123", "email": "user@example.com" } }',
      },
    ];
  }

  private async discoverComponents(): Promise<ComponentDoc[]> {
    // Discover React components from the components directory
    return [
      {
        name: 'Button',
        description: 'A customizable button component',
        props: [
          { name: 'variant', type: 'string', description: 'Button style variant' },
          { name: 'size', type: 'string', description: 'Button size' },
          { name: 'onClick', type: 'function', description: 'Click handler' },
        ],
        example: '<Button variant="primary" size="lg" onClick={handleClick}>Click me</Button>',
      },
    ];
  }
}

interface DocumentationFile {
  filename: string;
  content: string;
  category: string;
}

interface APIRoute {
  method: string;
  path: string;
  description: string;
  parameters: Parameter[];
  exampleRequest: string;
  exampleResponse: string;
}

interface Parameter {
  name: string;
  type: string;
  description: string;
}

interface ComponentDoc {
  name: string;
  description: string;
  props: PropDoc[];
  example: string;
}

interface PropDoc {
  name: string;
  type: string;
  description: string;
}

// Usage
const generator = new DocumentationGenerator();
generator.generateComprehensiveDocs();
```

## Best Practices

### 1. Context7 Integration Best Practices

- **Library Selection**: Always choose libraries with high trust scores and comprehensive code snippets
- **Documentation Scope**: Request focused documentation by specifying relevant topics
- **Token Management**: Balance documentation depth with token limits for optimal results
- **Validation**: Always validate generated code against the retrieved documentation

### 2. TaskMaster-AI Integration Best Practices

- **Task Granularity**: Create specific, actionable tasks with clear acceptance criteria
- **Automation Setup**: Configure automated workflows early in the project lifecycle
- **Code Review Integration**: Leverage AI code review for consistent quality standards
- **Performance Monitoring**: Set up continuous monitoring of AI-generated suggestions

### 3. Development Workflow Optimization

- **Documentation-First**: Generate documentation before implementation
- **Continuous Integration**: Integrate AI tools into CI/CD pipelines
- **Regular Analysis**: Schedule regular codebase analysis and improvement suggestions
- **Team Collaboration**: Share AI insights across development team members

### 4. Quality Assurance

- **Multi-Source Validation**: Cross-reference AI suggestions with official documentation
- **Incremental Implementation**: Implement AI suggestions incrementally with proper testing
- **Human Oversight**: Maintain human review for critical business logic and security components
- **Feedback Loops**: Provide feedback to improve AI tool accuracy over time

This comprehensive tools integration guide provides you with the framework to leverage Context7 and TaskMaster-AI effectively in your NextJS development workflow, enhancing productivity while maintaining code quality and best practices.
