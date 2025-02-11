node {
    stage('CheckSCM') {
        checkout scm
    }
    stage('Build') {
        docker.image('python:3.9').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        junit 'test-reports/results.xml'
    }
    stage('Manual-Approval') {
        input message: 'Lanjutkan ke tahap Deploy?'
    }
    stage('Deploy') {
        // Code for localhost deployment
        //docker.image('python:3.9').inside('--user root') {
        //    sh 'pip install pyinstaller'
        //    sh 'pyinstaller --onefile sources/add2vals.py'
        //    sleep time: 1, unit: 'MINUTES'
        //}
        //archiveArtifacts 'dist/add2vals'
        // Code for deploying to EC2
        docker.image('python:3.9').inside('--user root') {
            sh 'pip install pyinstaller'
            sh 'pyinstaller --onefile sources/add2vals.py'
        //    sleep time: 1, unit: 'MINUTES'
			archiveArtifacts 'dist/add2vals'
		// Deploy app to EC2
			sh 'scp -o StrictHostKeyChecking=no -i /home/jenkins/pem/ec2-cicd-phy-aks.pem dist/add2vals ubuntu@ec2-13-229-83-98.ap-southeast-1.compute.amazonaws.com:/home/ubuntu/'
		//	sh 'scp -i /home/jenkins/pem/ec2-cicd-phy-aks.pem dist/add2vals ubuntu@ec2-13-229-83-98.ap-southeast-1.compute.amazonaws.com:/home/ubuntu/'    
        // Run the app in EC2
			sh 'ssh -o StrictHostKeyChecking=no -i /home/jenkins/pem/ec2-cicd-phy-aks.pem ubuntu@ec2-13-229-83-98.ap-southeast-1.compute.amazonaws.com "chmod a+x /home/ubuntu/add2vals && /home/ubuntu/./add2vals 3 4"'
		//	sh 'ssh -i /home/jenkins/pem/ec2-cicd-phy-aks.pem ubuntu@ec2-13-229-83-98.ap-southeast-1.compute.amazonaws.com "chmod a+x /home/ubuntu/add2vals && /home/ubuntu/./add2vals 3 4"'
            sleep time: 1, unit: 'MINUTES'
        }
    }
}