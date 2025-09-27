# URGENT: Fix Critical Production Issue - SuperChat Code Application Down

## Context
You are being asked to fix a critical production issue where the SuperChat Code application at https://code.mbo9.com is returning 502 Bad Gateway errors. This application was working perfectly until yesterday when another Claude instance tried to fix an issue and broke everything.

## Your Mission
1. **Analyze all provided Kubernetes configurations and logs**
2. **Identify the root cause of the 502 error**
3. **Provide exact kubectl commands to fix the issue**
4. **Ensure the fix doesn't break anything else**

## Current Symptoms
- Application URL: https://code.mbo9.com returns 502 Bad Gateway
- Pods show as "Running" but application is not accessible
- OAuth2 proxy is working (redirects to Google login)
- After login, returns 502 error

## What Changed (Breaking Changes)
1. The `superchat-nginx-config` ConfigMap was missing and was recreated manually
2. The deployment had duplicate volume mounts that were "fixed" by removing one
3. The nginx configuration might not be correct

## Technical Stack
- **Kubernetes**: EKS on AWS (eu-west-2)
- **Ingress**: AWS Application Load Balancer
- **Authentication**: OAuth2 Proxy with Google
- **Backend**: FastAPI on port 8001
- **Frontend**: Next.js on port 3000
- **Reverse Proxy**: Nginx on port 8000
- **Container**: Uses supervisord to run multiple processes

## Expected Traffic Flow
1. User visits https://code.mbo9.com
2. CloudFront -> ALB Ingress
3. ALB -> OAuth2 Proxy Service (port 4180)
4. OAuth2 Proxy -> Superchat-Code Service (port 8000)
5. Nginx (port 8000) -> Backend (8001) or Frontend (3000)

## Critical Files to Review
1. **superchat-deployment.yaml** - Check volume mounts, container configuration
2. **all-configmaps.yaml** - Verify nginx configuration is correct
3. **superchat-pod-logs.txt** - Check for nginx errors
4. **oauth-proxy-logs.txt** - Verify OAuth is working
5. **superchat-service.yaml** - Ensure service targets correct port

## Specific Questions to Answer
1. Is the nginx ConfigMap correctly formatted and mounted?
2. Are the volume mounts in the deployment correct?
3. Is nginx actually running and listening on port 8000?
4. Is the service correctly routing to port 8000?
5. Are there any init container issues?

## Required Output
Please provide:
1. **Root cause analysis** - What exactly is broken
2. **Step-by-step fix** - Exact kubectl commands to run
3. **Verification steps** - How to confirm it's working
4. **Prevention** - What to avoid in future

## Important Constraints
- Cannot rebuild/redeploy from scratch (would lose current state)
- Must fix in-place using kubectl commands
- Cannot access EC2 instances directly (Fargate)
- Must maintain current OAuth configuration

## Previous Working Configuration Notes
The application WAS working with:
- Nginx reverse proxy handling requests on port 8000
- Proper ConfigMap with nginx configuration
- Correct volume mounts for all components
- Working OAuth2 authentication flow

## URGENT: Time Sensitive
This is a production outage affecting users. Please provide a solution that can be implemented immediately with kubectl commands. Focus on getting the application working first, optimizations can come later.

## Test After Fix
After applying your fix, we should be able to:
1. Visit https://code.mbo9.com
2. Get redirected to Google OAuth
3. After login, see the application (not 502)
4. Access both frontend and backend endpoints

Please analyze all the provided files carefully and give us the exact commands to restore service!