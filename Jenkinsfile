pipeline {
  agent {label '10-node'}
  environment {
    REGISTRY_URL = 'http://ops-cy-245:9998/'
    IMAGE_NAME = 'ops-cy-245:9998/library/java-project-school'
    CREDENTIALS_ID = 'harbor-account' // Jenkins 中存储的 Harbor 凭证 ID
  }
  
  stages {
    stage('Checkout') { // git拉取代码
	  steps {
	    git branch: 'master', url: 'https://gitee.com/dy5/smallschool.git'
	  }
	}
	stage('Docker Build') { // 根据 Docker Compose 文件构建镜像
      steps {
        script {
		  docker.build("ops-cy-245:9998/library/go-project:${env.BUILD_ID}")
		
          // 使用 Docker Compose 构建镜像
          //withEnv(["BUILD_ID=${env.BUILD_ID}"]) {
          //  sh "docker-compose -f docker-compose.yml build"
		  }
        }
      }
    }
	
	
	
	
	stage('Push to Harbor') {  // 上传镜像至harbor
      steps {
        script {  // 'harbor-credentials' 是存储在 Jenkins 中的凭证 ID
          docker.withRegistry('${REGISTRY_URL}', '${CREDENTIALS_ID}') { // 指定 Docker 仓库的 URL 和认证凭证
            docker.image("${IMAGE_NAME}:${env.BUILD_ID}").push()
		    // 将构建好的Docker镜像推送到指定的Harbor仓库。docker.image方法返回一个Docker镜像对象，push()方法将其推送到 Docker 仓库。
          }
        }
      }
    }
	stage('docker run') {
	  steps {
	    script {
          // 使用 Docker 容器运行构建好的镜像
          sh "docker run -d --name java-project-school -p 8090:8080 ${IMAGE_NAME}:${env.BUILD_ID}"
        }
	  }
	}
  }
}
