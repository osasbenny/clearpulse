# ClearPulse - cPanel Deployment Guide

## Overview
This guide explains how to deploy the ClearPulse banking platform to a cPanel hosting environment.

## Prerequisites
- cPanel hosting account with Node.js support
- MySQL database
- SSH access (recommended) or File Manager access
- Domain or subdomain configured

## Deployment Steps

### 1. Database Setup
1. Log in to cPanel
2. Navigate to **MySQL Databases**
3. Create a new database (e.g., `clearpulse_db`)
4. Create a database user with a strong password
5. Grant all privileges to the user for the database
6. Note down the database credentials:
   - Database Host: `localhost` (or provided by host)
   - Database Name: `your_username_clearpulse_db`
   - Database User: `your_username_dbuser`
   - Database Password: `your_password`

### 2. Upload Files

#### Option A: Using Git (Recommended)
```bash
# SSH into your cPanel account
cd public_html  # or your domain directory
git clone https://github.com/osasbenny/clearpulse.git .
```

#### Option B: Using File Manager
1. Download the repository as ZIP from GitHub
2. Upload to cPanel File Manager
3. Extract in your domain directory (e.g., `public_html`)

### 3. Configure Environment Variables
1. In cPanel, navigate to **Setup Node.js App**
2. Create a new application or edit existing
3. Set the following environment variables:

```env
NODE_ENV=production
DATABASE_URL=mysql://username:password@localhost:3306/database_name

# JWT Secret (generate a random string)
JWT_SECRET=your-super-secret-jwt-key-here

# OAuth Configuration (if using Manus OAuth)
OAUTH_SERVER_URL=https://api.manus.im
VITE_OAUTH_PORTAL_URL=https://oauth.manus.im
VITE_APP_ID=your-app-id
OWNER_OPEN_ID=your-owner-openid
OWNER_NAME=Your Name

# Analytics (optional)
VITE_ANALYTICS_ENDPOINT=https://analytics.example.com
VITE_ANALYTICS_WEBSITE_ID=your-website-id

# Forge API (if using Manus services)
BUILT_IN_FORGE_API_URL=https://forge.manus.im
BUILT_IN_FORGE_API_KEY=your-forge-api-key
VITE_FRONTEND_FORGE_API_KEY=your-frontend-api-key
VITE_FRONTEND_FORGE_API_URL=https://forge.manus.im
```

### 4. Install Dependencies
```bash
cd /home/username/public_html
npm install -g pnpm
pnpm install --prod
```

### 5. Run Database Migrations
```bash
pnpm db:push
```

### 6. Configure Node.js Application in cPanel
1. Navigate to **Setup Node.js App**
2. Click **Create Application**
3. Configure:
   - **Node.js version**: 22.x or latest LTS
   - **Application mode**: Production
   - **Application root**: `/home/username/public_html`
   - **Application URL**: Your domain
   - **Application startup file**: `dist/index.js`
   - **Passenger log file**: Enable for debugging

### 7. Configure .htaccess (if needed)
Create or edit `.htaccess` in your domain root:

```apache
# Redirect all requests to Node.js app
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ http://localhost:YOUR_PORT/$1 [P,L]

# Enable CORS if needed
<IfModule mod_headers.c>
    Header set Access-Control-Allow-Origin "*"
</IfModule>
```

Replace `YOUR_PORT` with the port assigned by cPanel Node.js setup.

### 8. Start the Application
1. In **Setup Node.js App**, click **Start App**
2. Monitor the logs for any errors
3. Visit your domain to verify deployment

## Static Files
The `dist/public` directory contains all frontend assets:
- `index.html` - Main HTML file
- `assets/` - JavaScript and CSS bundles
- `images/` - Banking images
- `logo.png` - ClearPulse logo
- `favicon.png` - Site favicon

These are served automatically by the Node.js server.

## Troubleshooting

### Application won't start
- Check Node.js version compatibility
- Verify all environment variables are set
- Check application logs in cPanel
- Ensure database connection is working

### Database connection errors
- Verify DATABASE_URL format: `mysql://user:pass@host:3306/dbname`
- Check database user has proper privileges
- Ensure MySQL is running

### Static files not loading
- Verify `dist/public` directory exists
- Check file permissions (755 for directories, 644 for files)
- Clear browser cache

### 502 Bad Gateway
- Application may have crashed - check logs
- Port may be blocked - contact hosting support
- Restart the Node.js application

## Performance Optimization
1. Enable cPanel's **Node.js Selector** caching
2. Configure **CloudFlare** or CDN for static assets
3. Enable **Gzip compression** in .htaccess
4. Set up **SSL certificate** (Let's Encrypt via cPanel)

## Maintenance
- Regularly update dependencies: `pnpm update`
- Monitor application logs
- Back up database regularly
- Keep Node.js version updated

## Support
For issues specific to:
- **ClearPulse Application**: Check GitHub issues
- **cPanel Configuration**: Contact your hosting provider
- **Database Issues**: Check cPanel MySQL logs

## Security Notes
- Never commit `.env` files to Git
- Use strong database passwords
- Keep JWT_SECRET secure and random
- Enable SSL/HTTPS for production
- Regularly update dependencies for security patches
