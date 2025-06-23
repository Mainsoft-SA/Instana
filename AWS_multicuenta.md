# **Monitoreo de multiples cuentas de AWS con Instana usando roles**
**AWS Security Token Service (AWS STS)**

1. Arquitectura

   ![image](https://github.com/user-attachments/assets/9be23fba-78b5-44af-ac90-27e443338617)

   Cuenta principal: AWS Perú
   
   Cuenta adicional: AWS Chile
   
3. Requisitos
   - Acceso admin a las cuentas de AWS
   - Contar con recursos a monitorear en ambas cuentas, para este ejemplo se utilizaron lambdas (https://github.com/juan-conde-21/aws_lambda)
   - Contar con el monitoreo del ec2, se requiere un agente por cada región
   - Contar con la instrumentación de los lambdas
   
4. El agente de instana tiene que estar en la cuenta principal (https://github.com/juan-conde-21/Instana-sensor-AWS-EC2)

5. Anotar el ARN del rol de cada cuenta

   |Cuenta principal: AWS Perú|Cuenta adicional: AWS Chile|
   |:---:|:---:|
   |![image](https://github.com/user-attachments/assets/4d6172c2-6fa2-44a3-a7c5-3a9a586b3c5f)|![image](https://github.com/user-attachments/assets/b6e59db0-5925-4943-bdbd-98d070229ebd)|
  
7. Modificar politicas y roles en cada cuenta

   **Cuenta principal: AWS Perú**

   **Política:**

   Agregar el ARN de la cuenta adicional

   ```
   {
			"Action": [
				"sts:AssumeRole"
			],
			"Resource": "arn:aws:iam::565393050348:role/instana-sensor-ec2-role",
			"Effect": "Allow"
		}
   ```
   ![image](https://github.com/user-attachments/assets/bf60e4c5-4f3d-4742-831b-dfc57ccbbf23)

   **Rol:**

   Agregar el ARN de la cuenta adicional

   ```
   {
			"Effect": "Allow",
			"Principal": {
				"AWS": "arn:aws:iam::565393050348:role/instana-sensor-ec2-role"
			},
			"Action": "sts:AssumeRole"
		}
   ```
   ![image](https://github.com/user-attachments/assets/4083a9df-813b-4630-b17c-78c0a076aa3c)


   **Cuenta adicional: AWS Chile**

   **Política:**

   Agregar el ARN de la cuenta principal

   ```
   {
			"Action": [
				"sts:AssumeRole"
			],
			"Resource": "arn:aws:iam::311141524250:role/instana-sensor-ec2-role",
			"Effect": "Allow"
		}
   ```
   ![image](https://github.com/user-attachments/assets/a53ebccf-9de9-497b-96d2-aa29cd2567b0)

   **Rol:**

   Agregar el ARN de la cuenta principal

   ```
   {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::311141524250:role/instana-sensor-ec2-role"
            },
            "Action": "sts:AssumeRole"
        }
   ```
   ![image](https://github.com/user-attachments/assets/46225206-542e-45c6-ad8e-3bf027e15ed7)
 
9. Modificar el configuration.yaml en el agente de Instana para agregar el ARN de la cuenta adicional

   ```
   com.instana.plugin.aws:
     role_arns:
       - 'arn:aws:iam::565393050348:role/instana-sensor-ec2-role'
   ```

   ![image](https://github.com/user-attachments/assets/a12485db-d3bc-42d6-9fa7-1e08e827f09c)


10. Validar en Instana

    ![image](https://github.com/user-attachments/assets/ae2e8e8d-fe6c-49fc-b73d-e458a77f1229)
    
    ![image](https://github.com/user-attachments/assets/6bcf27e1-146c-4bcc-b74b-4f222d178b13)
    
Referencia:
- https://github.com/juan-conde-21/Instana-sensor-AWS-EC2
- https://github.com/juan-conde-21/aws_lambda
- https://www.ibm.com/docs/en/instana-observability/current?topic=agents-amazon-web-services-aws#aws-sts-approach
