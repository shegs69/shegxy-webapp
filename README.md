# Shegxy Tasks

A premium, dark-mode task management app built on a fully serverless AWS architecture. Features glassmorphic UI design, priority levels, due dates, categories, and real-time notifications.

## ✨ Features

- **Premium Dark UI** — Glassmorphic design system with violet accent palette, smooth animations, and Inter typography
- **Task Priorities** — Color-coded priority levels (Low, Medium, High, Urgent) with visual indicators
- **Due Dates** — Track deadlines with overdue detection and visual alerts
- **Categories** — Free-form tags to organize tasks by context
- **Dashboard Stats** — Real-time stat cards showing Total, Pending, Completed, and Overdue counts
- **Search & Filter** — Client-side search with status, priority, and category filters
- **Authentication** — AWS Cognito integration with managed sign-in/sign-up
- **Real-time Updates** — Async job notifications via AppSync Events
- **Translation** — Built-in translation job powered by AWS Translate via Lambda

## Tech Stack

| Layer | Technology |
|-------|-----------|
| **Frontend** | Next.js 16 (App Router, Turbopack), React 19, Tailwind CSS v4 |
| **Backend** | Next.js Server Actions, Zod validation, Prisma ORM |
| **Database** | PostgreSQL on Aurora Serverless v2 |
| **Auth** | AWS Cognito |
| **Infra** | AWS CDK, Lambda, CloudFront, AppSync Events |
| **UI** | Lucide icons, Sonner toasts, shadcn/ui components |

## Architecture

![architecture](./.serverless-full-stack-webapp-starter-kit/docs/imgs/architecture.png)

| Service | Role |
|---------|------|
| [Aurora PostgreSQL Serverless v2](https://aws.amazon.com/rds/aurora/serverless/) | Relational database with Prisma ORM |
| [Next.js App Router](https://nextjs.org/docs/app) on [Lambda](https://aws.amazon.com/lambda/) | Unified frontend and backend |
| [CloudFront](https://aws.amazon.com/cloudfront/) + Lambda Function URL | Content delivery with response streaming |
| [Cognito](https://aws.amazon.com/cognito/) | Authentication (email by default, OIDC federation supported) |
| [AppSync Events](https://docs.aws.amazon.com/appsync/latest/eventapi/event-api-welcome.html) + Lambda | Async jobs and real-time notifications |
| [EventBridge](https://aws.amazon.com/eventbridge/) | Scheduled jobs |
| [CloudWatch](https://aws.amazon.com/cloudwatch/) + S3 | Access logging |
| [CDK](https://aws.amazon.com/cdk/) | Infrastructure as Code |

Fully serverless — high cost efficiency, scalability, and minimal operational overhead.

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) (>= v20)
- [Docker](https://docs.docker.com/get-docker/)
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) with a configured IAM profile

### 1. Install dependencies

```sh
cd webapp
npm install
```

### 2. Deploy

```sh
cd cdk
npm ci
npx cdk bootstrap
npx cdk deploy --all
```

Initial deployment takes about 20 minutes. After success, you'll see:

```
 ✅  ServerlessWebappStarterKitStack

Outputs:
ServerlessWebappStarterKitStack.FrontendDomainName = https://web.example.com
ServerlessWebappStarterKitStack.DatabaseSecretsCommand = aws secretsmanager get-secret-value ...
ServerlessWebappStarterKitStack.DatabasePortForwardCommand = aws ssm start-session ...
```

Open the `FrontendDomainName` URL to access the app.

### 3. Run database migration

After deploying, run the migration to add the new fields:

```sh
cd webapp
npx prisma migrate dev --name add-priority-duedate-category
```

### 4. Local development

```sh
cd webapp
npm run dev
```

The app runs on [http://localhost:3010](http://localhost:3010) with Turbopack.

## Project Structure

```
├── cdk/                  # AWS CDK infrastructure
├── webapp/
│   ├── prisma/           # Database schema & migrations
│   └── src/
│       ├── app/
│       │   ├── (root)/           # Main dashboard
│       │   │   ├── components/   # Todo components
│       │   │   ├── actions.ts    # Server actions
│       │   │   ├── schemas.ts    # Zod schemas
│       │   │   └── page.tsx      # Dashboard page
│       │   ├── sign-in/          # Sign-in page
│       │   ├── globals.css       # Design system
│       │   └── layout.tsx        # Root layout
│       ├── components/           # Shared components
│       ├── hooks/                # Custom hooks
│       └── lib/                  # Utilities & config
├── docs/
│   ├── WALKTHROUGH.md            # Detailed change walkthrough
│   └── IMPLEMENTATION_PLAN.md    # Technical implementation plan
└── AGENTS.md                     # Development guide
```

## Documentation

- **[WALKTHROUGH.md](docs/WALKTHROUGH.md)** — Detailed walkthrough of all changes made during the premium makeover
- **[IMPLEMENTATION_PLAN.md](docs/IMPLEMENTATION_PLAN.md)** — Technical implementation plan with proposed changes and verification steps
- **[AGENTS.md](AGENTS.md)** — Development guide for local setup, authentication, async jobs, DB migration, and coding conventions

## Cost

Sample cost breakdown for us-east-1, one month, with cost-optimized configuration:

| Service | Usage Details | Monthly Cost [USD] |
|---------|--------------|-------------------|
| Aurora Serverless v2 | 0.5 ACU × 2 hour/day, 1GB storage | 3.6 |
| Cognito | 100 MAU | 1.5 |
| AppSync Events | 100 events/month, 10 hours connection/user/month | 0.02 |
| Lambda | 1024MB × 200ms/request | 0.15 |
| Lambda@Edge | 128MB × 50ms/request | 0.09 |
| VPC | NAT Instance (t4g.nano) × 1 | 3.02 |
| EventBridge | Scheduler 100 jobs/month | 0.0001 |
| CloudFront | Data transfer 1kB/request | 0.01 |
| **Total** | | **8.49** |

Assumes 100 users/month, 1000 requests/user. Costs could be further reduced with [Free Tier](https://aws.amazon.com/free/).

## Clean Up

```sh
cd cdk
npx cdk destroy --force
```

---

© 2026 Shegxy. Built with ✨
