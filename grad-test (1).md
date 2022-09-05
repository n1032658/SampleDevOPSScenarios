```
1. Write a Dockerfile to run nginx version 1.19 in a container.

Choose a base image of your liking.

The build should be security conscious and ideally pass a container
image security test. [20 pts]
```

Through command line:
sudo  su -
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
docker --version

config file:

server {
    listen 80 default_server;
    listen [::]:80 default_server;
    
    root /usr/share/nginx/html;
    index index.html index.htm;

    server_name _;
    location / {
        try_files $uri $uri/ =404;
    }
}

vim Dockerfile



# Pull the minimal Ubuntu image
FROM ubuntu

# Install Nginx
RUN apt-get -y update && apt-get -y install nginx=1.19.*

# Copy the Nginx config
COPY default /etc/nginx/sites-available/default

# Expose the port for access
EXPOSE 80/tcp

# Run the Nginx server
CMD ["/usr/sbin/nginx", "-g", "daemon off;"]

:wq
//creating image
docker build -t  mynginx  .
//creating container from the image
docker run --name mynginxcontainer -d -p 80:80 mynginx

For checking wheather nginx installed correctly or not
http://54.152.46.15:80

docker scan mynginx



```
2.  Write a Kubernetes StatefulSet to run the above, using persistent volume claims and
resource limits. [15 pts]
```
vim pod-definition1.yml

---
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1beta1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.19
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      # persistentVolumeReclaimPolicy: Retain
      resources:
        requests:
          storage: 1Gi
```
3. Write a simple build and deployment pipeline for the above using groovy /
Jenkinsfile, CircleCI, GitLab, or GitHub Actions. [15 pts]
```

Requiremtns: Install docker, docker plugins in Jenkins, Add Jenkins user to the docker group

Groovy script:

node('master'){
 
 stage('Continuous Download') 
   
	 {
	
  checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '7d0c66bf-4a63-4385-b6b0-af1d614e247c', url: 'https://github.com/n1032658/MyJenkinsDockerExample.git']]])	}
	
    stage("build") {
 
      sh 'docker build -t  mynginx  .'
      
    }
    stage("run") {
   sh 'docker run --name mynginxcontainer -d -p 80:80 mynginx'
    
    }
  
}







```
4. Source or come up with a text manipulation problem and solve it with at least two of
awk, sed, tr and / or grep. Check the question below first though, maybe. [10pts]
```
s for substitute, g for global if g is not specified it only works once per line

cat testfile | tr " " "*"
cat testfile | sed ‘s/\s+//g’
cat testfile | sed 's/\s+//g' | tr " " "*"


```
5. Solve the problem in question 4 using any programming language you like. [15pts]
```
import java.util.*;
class Main {
    public static void main(String[] args) {
        System.out.println("enter a string");
        Scanner sc=new Scanner(System.in);
        String s=sc.nextLine();
        System.out.println("removed whitespaces "+s.replaceAll("\\s+",""));
        
    }
}
```




6. The Fibonacci sequence is defined as a sequence of integers starting with 1 and 1, 
where each subsequent value is the sum of the preceding two. I.e. f(0) = 1 f(1) = 1 f(n) = f(n-1) + f(n-2) where n >= 2
 Write a program in a language of your choice to calculate the sum of the first 100 even-valued Fibonacci numbers 2. [20pts]
```

import java.math.BigInteger;
public class Main
{
	public static void main(String[] args) {
	BigInteger a = BigInteger.ZERO;
BigInteger b = BigInteger.ONE;

BigInteger sumOfEven= BigInteger.ZERO;
int count = 0;
BigInteger temp;
while( count < 100) {
    temp = b.add(a);
  
    if (temp.mod(BigInteger.TWO).equals(BigInteger.ZERO)) {
        sumOfEven = sumOfEven.add(temp);
        count++;
    }
    b = a;
    a = temp;
}
System.out.println("sum of the first 100 even-valued Fibonacci numbers "+sumOfEven);
	}
}


```
7. Write a function in a language of your choice which, when passed two sorted arrays of integers returns an array of any numbers 
which appear in both. No value should appear in the returned array more than once. [15 pts]
```

import java.util.*;

class Combine {
    
    static int combine(int arr1[], int arr2[], int m, int n)
    {
        int i = 0, j = 0;
        while (i < m && j < n) {
		
		//if array1 element is smaller print array1 element and increment i
            if (arr1[i] < arr2[j])
                System.out.print(arr1[i++] + " ");
				//if array2 element is smaller print array2 element and increment j
            else if (arr2[j] < arr1[i])
                System.out.print(arr2[j++] + " ");
            else {
			//if equal, print one of the value and increment both values
                System.out.print(arr2[j++] + " ");
                i++;
            }
        }
 
   // print the remaining values in the array
        while (i < m)
            System.out.print(arr1[i++] + " ");
        while (j < n)
            System.out.print(arr2[j++] + " ");
 
        return 0;
    }
 
    public static void main(String args[])
    {
        
         Scanner sc = new Scanner(System.in);
      System.out.println("Enter the size of the array that is to be created::");
      int size1 = sc.nextInt();
      int[] arr1 = new int[size1];
      System.out.println("Enter the elements of the array ::");

      for(int i=0; i<size1; i++) {
         arr1[i] = sc.nextInt();
      }
       System.out.println("Enter the size of the array that is to be created::");
      int size2 = sc.nextInt();
      int[] arr2 = new int[size2];
      System.out.println("Enter the elements of the array ::");

      for(int i=0; i<size2; i++) {
         arr2[i] = sc.nextInt();
      }
      
        combine(arr1, arr2, size1, size2);
    }
}

```
8. Write a function in a language of your choice which, when passed a decimal digit X, returns the value of X+XX+XXX+XXXX. 
E.g. if the supplied digit is 3 it should return 3702 (3+33+333+3333). [15pts]
```
	

import java.util.*;
public class Main
{
	public static void main(String[] args) {
	Scanner sc=new Scanner(System.in);	
     int n=4;
    int sum=0,i;
    System.out.println("enter the decimal X value");
    int val=sc.nextInt();
   
    for(i=1;i<=4;i++)
    {
            sum+=val;
            val=(val*10)+3;
    }
   System.out.println("The sum of the series "+sum);

	}
}
