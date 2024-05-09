构建和推送 Docker 镜像到 Azure Container Registry
#### step1:登录到 Azure CLI，并选择要使用的 Azure 订阅
    az login
    az account set --subscription <subscription_id>
#### step2:构建 Docker 镜像并推送到 Azure 容器注册表（Azure Container Registry）
     # 登录到 Azure Container Registry
     az acr login --name <your_container_registry_name>

     # 构建 Docker 镜像
     docker build -t <registry_name>.azurecr.io/<image_name>:<tag> .

     # 推送 Docker 镜像到 Azure 容器注册表
     docker push <registry_name>.azurecr.io/<image_name>:<tag>

在 Azure 上部署 Container App
 
     1.在 Azure 门户中打开 Azure 容器实例 (ACI) 服务。
     2.点击“创建容器实例”，然后填写必要的信息：
       容器名称：指定容器实例的名称。
       镜像类型：选择“私有注册表”并选择你的 Azure 容器注册表。
       镜像：输入之前推送的 Docker 镜像路径（例如 registry_name.azurecr.io/image_name:tag）。
       资源组：选择一个资源组或创建新的资源组。
       定价层：选择合适的定价层。
     3.完成部署后，Azure 会创建并启动你的容器实例，并为其分配一个公共 IP 地址。
     4.一旦容器实例启动完成，你就可以通过该实例的公共 IP 地址访问你的 Go 应用程序。
注意事项

    在部署时，请确保在 Azure 容器实例 (ACI) 服务中选择正确的定价层，以符合你的使用需求和预算。
    请注意 Dockerfile 中的镜像路径和名称，确保与你的 Azure 容器注册表中的设置一致。