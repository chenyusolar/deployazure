部署 Github仓库代码自动部署到Azure的容器应用(Container APP).

Step1: 准备 Azure 相关资源 
    
     1. 创建 Azure 容器注册表：
        在 Azure 门户中创建一个容器注册表 (Azure Container Registry)，用于存储你的 Docker 镜像。
     2. 获取 Azure 访问凭据：
        在 Azure Portal 中获取容器注册表的登录凭据（用户名和密码），用于 GitHub Actions 访问该容器注册表。
Step2: 配置 GitHub 仓库
     
      1. 在 GitHub 仓库中添加 Secrets：
         在 GitHub 仓库的 Settings > Secrets 页面中，添加以下 Secrets：
            REGISTRY_U
SERNAME: 容器注册表的用户名
            REGISTRY_PASSWORD: 容器注册表的密码

Step3: 编写 GitHub Actions Workflow
      
      在 GitHub 仓库的 .github/workflows 目录下创建一个 YAML 文件，例如 deploy-to-azure.yml，编写以下内容：


  ```
    name: Deploy to Azure Container App

    on:
      push:
        branches:
          - main

    jobs:
      build-and-deploy:
         runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Log in to Azure Container Registry
        uses: azure/docker-login@v1
        with:
          login-server: <your_registry_name>.azurecr.io
          username: ${{ secrets.REGISTRY_USERNAME }}
          password: ${{ secrets.REGISTRY_PASSWORD }}

      - name: Build Docker image
        run: |
          docker build --file Dockerfile --tag <your_registry_name>.azurecr.io/<image_name>:latest .

      - name: Push Docker image to Azure Container Registry
        run: |
          docker push <your_registry_name>.azurecr.io/<image_name>:latest

      - name: Deploy to Azure Container Instance
        uses: azure/aci-deploy@v1
        with:
          resource-group: <your_resource_group_name>
          dns-name-label: <dns_name_label>
          image-name: <your_registry_name>.azurecr.io/<image_name>:latest
          registry-login-server: <your_registry_name>.azurecr.io
          registry-username: ${{ secrets.REGISTRY_USERNAME }}
          registry-password: ${{ secrets.REGISTRY_PASSWORD }}
          container-command: ['<your_executable>']
    
  ```

  #### 替换参数说明：
     <your_registry_name>: 替换为你的 Azure 容器注册表名称。
     <image_name>: 替换为你的 Docker 镜像名称。
     <your_resource_group_name>: 替换为你的 Azure 资源组名称，用于部署容器实例。
     <dns_name_label>: 替换为 Azure 容器实例的 DNS 名称标签。
     <your_executable>: 替换为你要在容器中执行的可执行文件名称。
  
  #### 工作流程说明：
     1.当代码提交到 main 分支时触发 GitHub Actions 工作流程。
     2.使用 Docker 构建器 (docker/setup-buildx-action@v1) 设置 Docker Buildx。
     3.使用 Azure 登录操作 (azure/docker-login@v1) 登录到 Azure 容器注册表。
     4.构建 Docker 镜像并推送到 Azure 容器注册表。
     5.使用 Azure 容器实例部署操作 (azure/aci-deploy@v1) 将镜像部署到 Azure 容器实例中。

  #### 注意事项：
     在 Dockerfile 中，确保配置了正确的 Go 程序构建命令。
     替换 YAML 文件中的 <placeholders> 为你的实际信息。
     配置好 Azure 相关资源和 GitHub Secrets。
     可根据实际需求调整 GitHub Actions 的触发条件和部署策略。
     
  这样配置后，每当你向 GitHub 仓库的 main 分支提交代码时，GitHub Actions 将自动构建 Docker 镜像并部署到 Azure 容器实例中，实现自动化部署流程。