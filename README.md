# gitlfs-proxy

A Cloudflare Pages/Workers proxy that enables Git LFS to use any S3-compatible storage backend (including Cloudflare R2, AWS S3, Backblaze B2, etc.) instead of your Git provider's built-in LFS storage.

## Why Use This?

- **Save money**: Use cheaper storage backends (R2 costs $0.015/GB vs GitHub's $0.07/GB)
- **Avoid bandwidth fees**: Many S3-compatible services have no egress charges
- **Work with any Git provider**: Including Bitbucket, GitLab, GitHub, or self-hosted Git
- **Keep existing workflow**: No changes to your Git LFS usage

## Quick Start

### 1. Deploy the Proxy

#### Option A: Use GitHub Integration (Recommended)
1. Fork this repository to your GitHub account
2. Log in to [Cloudflare Dashboard](https://dash.cloudflare.com)
3. Go to **Workers & Pages** → **Create application** → **Pages** → **Connect to Git**
4. Select your forked repository
5. Set **Build command** to `npm install`
6. Deploy

#### Option B: Use Wrangler CLI
```bash
git clone https://github.com/yourusername/gitlfs-proxy.git
cd gitlfs-proxy
npm install
npm install -g wrangler
wrangler login
wrangler pages deploy . --project-name gitlfs-proxy
```

### 2. Set Up Your Storage

Create a bucket on your preferred S3-compatible storage provider:

- **Cloudflare R2** (Recommended - generous free tier)
- **AWS S3** 
- **Backblaze B2**
- **Wasabi**
- **DigitalOcean Spaces**

Create an access key with read/write permissions to your bucket.

### 3. Configure Your Repository

Replace the values below with your actual credentials and bucket information:

```bash
# Navigate to your Git repository
cd your-repo

# Configure Git LFS to use your proxy
git config -f .lfsconfig lfs.url 'https://ACCESS_KEY:SECRET_KEY@your-proxy.pages.dev/ENDPOINT/BUCKET_NAME'

# For Cloudflare R2, the format is:
git config -f .lfsconfig lfs.url 'https://ACCESS_KEY:SECRET_KEY@your-proxy.pages.dev/ACCOUNT_ID.r2.cloudflarestorage.com/BUCKET_NAME'

# Commit the configuration
git add .lfsconfig
git commit -m "Configure custom LFS server"
```

## Example URLs

### Cloudflare R2
```
https://ed41437d53a69dfc:dc49cbe38583b850a7454c89d74fcd51@your-proxy.pages.dev/7795d95f5507a0c89bd1ed3de8b57061.r2.cloudflarestorage.com/my-lfs-bucket
```

### AWS S3
```
https://AKIAIOSFODNN7EXAMPLE:wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY@your-proxy.pages.dev/s3.amazonaws.com/my-lfs-bucket
```

### Backblaze B2
```
https://your-app-key-id:your-app-key@your-proxy.pages.dev/s3.us-west-002.backblazeb2.com/my-lfs-bucket
```

## URL Format

```
https://<ACCESS_KEY_ID>:<SECRET_ACCESS_KEY>@<YOUR_PROXY_DOMAIN>/<S3_ENDPOINT>/<BUCKET_NAME>
```

Where:
- `ACCESS_KEY_ID` & `SECRET_ACCESS_KEY`: Your storage provider credentials
- `YOUR_PROXY_DOMAIN`: Your deployed proxy URL (e.g., `my-proxy.pages.dev`)
- `S3_ENDPOINT`: Your storage provider's S3-compatible endpoint
- `BUCKET_NAME`: Your bucket name

## Security Notes

- **Public repositories**: Create read-only access keys for the `.lfsconfig` file, use read/write keys locally
- **Private repositories**: You can use read/write keys in `.lfsconfig` if all collaborators should have access
- **URL encoding**: If your credentials contain special characters, URL-encode them

## Troubleshooting

### "Object does not exist" errors
- Verify your bucket name and endpoint are correct
- Check that your access keys have the right permissions
- Ensure the bucket exists and is accessible

### "Authentication failed" errors
- Double-check your access key ID and secret key
- Verify the keys have read/write permissions to the bucket
- Try URL-encoding your credentials if they contain special characters

### "Repository not found" errors
- Make sure your `.lfsconfig` file is committed and pushed
- Verify the proxy URL is accessible
- Check that Git LFS is installed (`git lfs version`)

## Development

This proxy is based on the original work at [milkey-mouse/git-lfs-s3-proxy](https://github.com/milkey-mouse/git-lfs-s3-proxy).

To modify the proxy behavior, edit `_worker.js` and redeploy.

## License

CC0 1.0 Universal (Public Domain) - see LICENSE file for details.

This means you can use, modify, and distribute this code however you want, with no restrictions.