pipeline{
	agent any
	environment
	{
	    scannerHome = tool name: 'sonar_scanner_dotnet',type :'hudson.plugins.sonar.MsBuildSQRunnerInstallation'
	}
	stages
	{
		stage('checkout')
		{
			steps
			{
				checkout scm
			}
		}
		stage('nuget'){
            steps{
                bat "dotnet restore WebApplication4\\WebApplication4.csproj"
            }
        }
	stage('run test cases'){
            steps{
                bat "dotnet test"
            }
        }
        stage('start sonarqube analysis'){
            steps{
                withSonarQubeEnv('Test_Sonar')
                {
                    bat 'dotnet "C:/Program Files (x86)/Jenkins/tools/hudson.plugins.sonar.MsBuildSQRunnerInstallation/sonar_scanner_dotnet/SonarScanner.MSBuild.dll" begin /k:"webappanalysis1" /n:"webappnagp1" /v:1.0'
                }
            }
        }
        stage('build'){
            steps{
                bat "dotnet build -c Release -o WebApplication4/app/build"
            }
        }
        stage('end sonarqube analysis'){
            steps{
                withSonarQubeEnv('Test_Sonar')
                {
                    bat 'dotnet "C:/Program Files (x86)/Jenkins/tools/hudson.plugins.sonar.MsBuildSQRunnerInstallation/sonar_scanner_dotnet/SonarScanner.MSBuild.dll" end'
                }
            }
        }
        stage('release artifacts'){
            steps{
                    bat 'dotnet publish -c Release -o WebApplication4/app/publish'
            }
        }
         stage('docker image'){
            steps{
                    bat 'docker build --no-cache -t dtr.nagarro.com:443/i_malwinderkaur_master:%BUILD_NUMBER% ./WebApplication4'
            }
        }
        stage('push to DTR'){
            steps{
                    bat 'docker push dtr.nagarro.com:443/i_malwinderkaur_master:%BUILD_NUMBER%'
            }
        }
        stage('docker deployment'){
            steps{
                    bat 'docker run --name c_malwinderkaur_master -d -p 6000:80 dtr.nagarro.com:443/i_malwinderkaur_master:%BUILD_NUMBER%'
            }
        }
         stage('helm deployment'){
            steps{
                 withEnv(['KUBECONFIG="C:/Users/malwinderkaur/.kube/config"']) 
                 {
                    bat '''
                    kubectl create ns malwinder-dotnet-%BUILD_NUMBER%
                    helm install devops-helm-dotnet-nagp malwinderchart --set image=dtr.nagarro.com:443/i_malwinderkaur_master:%BUILD_NUMBER% --set nodePort=30157
                    '''
                 }
            }
        }

	}
}
