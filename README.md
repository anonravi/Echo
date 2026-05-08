# EchoGO - Voice Generation Platform

EchoGO is a professional voice generation platform that enables organizations to create high-quality voice recordings using AI technology.

## Features

- **Admin Dashboard**: Manage users, voice models, view generation history, and configure system settings
- **Employee Dashboard**: Generate voice audio, access generation history, and manage personal settings
- **Voice Model Management**: Add and manage voice models with custom descriptions and audio samples
- **User Management**: Create and manage employee accounts with role-based access control
- **Database Integration**: SQLite database for persistent data storage
- **Modern UI**: Built with Next.js, Tailwind CSS, and shadcn/ui components

## Installation

1. Clone the repository:
   ```
   git clone <repository-url>
   cd echo-of-goats
   ```

2. Install dependencies:
   ```
   npm install
   ```

3. Initialize the database:
   ```
   npm run db:init
   ```

4. Start the development server:
   ```
   npm run dev
   ```

5. Access the application at `http://localhost:9002`

## Development Credentials

During development, you can use these credentials:
- Admin: `admin_user` / `admin123`
- Employee: `employee_user` / `employee123`

## Production Setup

Before deploying to production, follow these steps to prepare the application:

1. Update the admin user and remove demo credentials:
   ```
   node scripts/update-admin-user.js
   ```

2. Alternatively, run the production preparation script which updates admin credentials and builds the application:
   ```
   node scripts/prepare-production.js
   ```

3. The default production admin credentials will be:
   - Username: `shashank`
   - Email: `vpsillusion@gmail.com`
   - Password: `greyhatkdo`

4. Deploy the application to your preferred hosting provider.

## Environment Variables

Create a `.env.local` file in the root directory with the following variables:

```
# Application
NEXT_PUBLIC_APP_URL=http://localhost:9002

# Authentication
NEXTAUTH_SECRET=your-secret-key
NEXTAUTH_URL=http://localhost:9002

# ElevenLabs API (Optional)
ELEVENLABS_API_KEY=your-elevenlabs-api-key
```

## Technologies Used

- Next.js 15.2
- React 18.3
- TypeScript
- Tailwind CSS
- shadcn/ui
- SQLite with better-sqlite3
- bcrypt for password hashing

## License

All rights reserved.
# Echo
