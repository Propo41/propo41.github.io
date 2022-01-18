# A personal portfolio

 A personal portfolio of myself made using React and the material UI library 🐴
- The deployed html files can be found [here](https://github.com/Propo41/propo41.github.io)
- The figma design can be found [here](https://www.figma.com/file/LwwsVZ1WRazesqS9A1DzSV/Resume-Website?node-id=501%3A325)
- Admin panel can be found [here](https://admin-personal-portfolio.netlify.app/)

Problems faced
---
- Upon page refresh, it returns 404 exception. Reason: Github doesn't allow SPA applications. [Fix](https://stackoverflow.com/a/63492458/7570616)

CI/CD deployment using github actions
---
The following script is used to build the react project folder and copy the contents of the build folder to this repository.

```yml
name: CI
# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the master branch
  push:
    branches: [ master ]
    
# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
 build-deploy:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        node-version: [16.x]
        
    steps:
    - name: Checkout repository
      uses: actions/checkout@v2
      with:
         token: ${{ secrets.ACTION_TOKEN }} 
         
    - name: Git version & config
      run: |
            echo "Git version"  
            git --version
            echo "Set config" 
            git config --local user.email "aliahnaf327@gmail.com"
            git config --local user.name  "propo41"
            echo "Git config"  
            git config --list
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v2
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
        
    - name: Install yarn
      run: npm install -g yarn
    - name: Install project packages
      run: yarn install --frozen-lockfile
    - name: Build for deployment
      run: yarn run build
      env: 
        REACT_APP_API_KEY: ${{ secrets.REACT_APP_API_KEY }}
        REACT_APP_AUTH_DOMAIN: ${{ secrets.REACT_APP_AUTH_DOMAIN }}
        REACT_APP_PROJECT_ID: ${{ secrets.REACT_APP_PROJECT_ID }}
        REACT_APP_STORAGE_BUCKET: ${{ secrets.REACT_APP_STORAGE_BUCKET }}
        REACT_APP_MESSAGING_SENDER_ID: ${{ secrets.REACT_APP_MESSAGING_SENDER_ID }}
        REACT_APP_APP_ID: ${{ secrets.REACT_APP_APP_ID }}
        REACT_APP_DB_URL: ${{ secrets.REACT_APP_DB_URL }}
      
    - name: Push build folder to another repository
      uses: cpina/github-action-push-to-another-repository@main
      env:
          API_TOKEN_GITHUB: ${{ secrets.ACTION_TOKEN }}
      with:
          source-directory: 'build'
          destination-github-username: 'propo41'
          destination-repository-name: 'propo41.github.io'
          user-email: aliahnaf327@gmail.com
          target-branch: master
      
    - name: Done
      run: |
        echo "🎉 The deployment was successfull!"  
```

References:
- https://github.com/marketplace/actions/push-directory-to-another-repository
- https://github.com/facebook/create-react-app/discussions/9064
- https://docs.github.com/en/actions/learn-github-actions/environment-variables
- https://pages.github.com/
