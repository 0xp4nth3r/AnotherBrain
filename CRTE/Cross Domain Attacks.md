
• Active Directory Certificate Services (AD CS) enables use of Public Key
Infrastructure (PKI) in active directory forest.
• AD CS helps in authenticating users and machines, encrypting and
signing documents, filesystem, emails and more.
• "AD CS is the Server Role that allows you to build a public key
infrastructure (PKI) and provide public key cryptography, digital
certificates, and digital signature capabilities for your organization."

###### Terminology

• CA - The certification authority that issues certificates. The server with AD CS
role (DC or separate) is the CA.
• Certificate - Issued to a user or machine and can be used for authentication,
encryption, signing etc.
• CSR - Certificate Signing Request made by a client to the CA to request a
certificate.
• Certificate Template - Defines settings for a certificate. Contains information
like - enrolment permissions, EKUs, expiry etc.
• EKU OIDs - Extended Key Usages Object Identifiers. These dictate the use of a
certificate template (Client authentication, Smart Card Logon, SubCA etc.)


###### Abuse

![[Pasted image 20250211215254.png]]


![[Pasted image 20250211215322.png]]




