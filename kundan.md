- š Hi, Iām @Saurav70
- š Iām interested in ...
- š± Iām currently learning ...
- šļø Iām looking to collaborate on ...
- š« How to reach me ...

<!---
Saurav70/Saurav70 is a āØ special āØ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
get char(include)
studio.main(void)
name: CI

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: Mono Build
        run: |
          chmod +x build.sh && ./build.sh
        
      - name: Upload Artifacts
        uses: actions/upload-artifact@v2
        with:
          name: library
          path: bin/autodeploytonugettest.dll
          retention-days: 1
  
  lint:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: Markdown Check
        run: |
          sudo chown -R $(whoami) /usr/local/bin /usr/local/lib /usr/local/include /usr/local/share
          npm install -g markdownlint-cli
          markdownlint *.md
      
      - name: Shell Check
        if: always()
        run: |
          sudo apt-get install shellcheck
          shellcheck *.sh
      - name: Download Artifacts
        uses: actions/download-artifact@v2
        if: always()
        with:
          name: library
          path: bin/autodeploytonugettest.dll
      
      - name: CSharp Check
        if: always()
        run: |
          sudo apt update
          sudo apt-get install gendarme
          chmod +r bin/
          cd bin/
          chmod +r autodeploytonugettest.dll
          gendarme -- autodeploytonugettest.dll
