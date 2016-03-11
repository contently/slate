### Deploying documentation changes

We use s3 to host the static documentation pages. Once you've made changes to the ```source/index.md``` file, take the following steps to deploy them:

1. Set your AWS credentials as environment variables:
```zsh
export AWS_ACCESS_KEY_ID=<AWS_KEY_ID>
export AWS_SECRET_ACCESS_KEY=<AWS_KEY>
```
2. Build the static files
```zsh
bundle exec middleman build
```
3. Sync the static files to s3 using middleman s3_sync:
```zsh
middleman s3_sync
```
4. Verify changes are visible on http://docs.contently.com/api/
