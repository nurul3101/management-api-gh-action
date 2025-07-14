# Prisma Postgres Database Management with GitHub Actions.

This project demonstrates automated database provisioning and management using Prisma Postgres Management API integrated with GitHub Actions workflows.

## Overview

The project automatically provisions temporary Prisma Postgres databases for pull requests and provides manual database management capabilities through GitHub Actions. This is useful for:

- Creating isolated database environments for each PR
- Testing database schemas and migrations
- Seeding databases with sample data
- Automatic cleanup when PRs are closed

## Features

- **Automatic Database Provisioning**: Creates a new Prisma Postgres database when a PR is opened
- **Database Seeding**: Automatically seeds the database with sample data using Prisma queries
- **Automatic Cleanup**: Deletes the database when a PR is closed or merged
- **Manual Control**: Trigger database creation/deletion manually from GitHub UI
- **Smart Naming**: Uses sanitized PR-based naming for databases
- **Error Handling**: Comprehensive error handling and validation

## Project Structure

```
├── .github/workflows/
│   └── ppg-database-management.yml    # GitHub Actions workflow
├── prisma/
│   └── schema.prisma                  # Prisma database schema
├── src/
│   ├── queries.ts                     # Database seeding script
│   └── caching.ts                     # Caching example
├── package.json                       # Node.js dependencies
└── README.md                          # This file
```

## Setup

### Prerequisites

- Prisma Postgres account
- GitHub repository with Actions enabled
- Node.js and npm

### Required GitHub Secrets

Configure the following secrets in your GitHub repository:

1. **PRISMA_POSTGRES_SERVICE_TOKEN**: Your Prisma Postgres service token
2. **PRISMA_PROJECT_ID**: Your Prisma project ID

### Installation

1. Clone the repository
2. Install dependencies:
   ```bash
   npm install
   ```
3. Configure your Prisma schema in `prisma/schema.prisma`
4. Set up your GitHub secrets

## Usage

### Automatic Workflow (Pull Requests)

The workflow automatically triggers on PR events:

- **PR Opened/Reopened**: Creates and seeds a new database
- **PR Closed/Merged**: Deletes the associated database

### Manual Workflow (GitHub UI)

1. Go to the **Actions** tab in your GitHub repository
2. Select **"Prisma Postgres Management API Workflow"**
3. Click **"Run workflow"**
4. Choose your action:
   - **provision**: Create and seed a new database
   - **cleanup**: Delete an existing database
5. Optionally specify a custom database name

## Database Schema

The project uses a simple blog-style schema with Users and Posts:

```prisma
model User {
  id      Int      @id @default(autoincrement())
  email   String   @unique
  name    String?
  posts   Post[]
}

model Post {
  id        Int     @id @default(autoincrement())
  title     String
  content   String?
  published Boolean @default(false)
  author    User?   @relation(fields: [authorId], references: [id])
  authorId  Int?
}
```

## Seeding

The database is automatically seeded with sample data using `src/queries.ts`:

- Creates sample users (Alice and Bob)
- Creates sample posts for each user
- Demonstrates various Prisma operations

## Scripts

- `npm run queries`: Run the database seeding script
- `npm run caching`: Run the caching example

## Workflow Details

### Database Naming

Databases are named using the format:
- **PR-based**: `pr-{pr_number}-{branch_name}` (sanitized)
- **Manual**: Custom name or `test-{run_number}`

### Error Handling

The workflow includes comprehensive error handling:
- Validates required secrets
- Handles API errors gracefully
- Provides detailed logging
- Implements retry logic for critical operations

### Security

- Uses GitHub secrets for sensitive tokens
- Sanitizes database names to prevent injection
- Implements proper authorization headers

## API Endpoints Used

The workflow interacts with these Prisma Postgres Management API endpoints:

- `GET /projects/{project_id}/databases` - List databases
- `POST /projects/{project_id}/databases` - Create database
- `POST /projects/{project_id}/databases/{db_id}/connections` - Create connection
- `DELETE /projects/{project_id}/databases/{db_id}` - Delete database

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with the manual workflow
5. Submit a pull request

## License

MIT License - see LICENSE file for details

---

## Original Prisma Example Features

This project also includes the original Prisma Postgres example capabilities:

- Schema migrations and queries (via [Prisma ORM](https://www.prisma.io/orm))
- Connection pooling and caching (via [Prisma Accelerate](https://prisma.io/data-platform/accelerate))

For local development and testing, you can still use the original features by setting up a `.env` file with your `DATABASE_URL` and running the scripts locally.
