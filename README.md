# Deploying a checksum runner:

### Step 1: Create a master database connector.
If you already have a connector setup for this database connection, skip this step.

Create an index.js file using the proper connector for your database type:
Starting by requiring the proper connector for your database type:
(i.e. leo-connector-sqlserver, leo-connector-postgres or leo-connector-mysql)

Example **index.js**:
```javascript
// use the connector for your database type:
const connector = require('leo-connector-mysql');

module.exports = connector.checksum({
	host: process.env.DB_HOST, // use server instead of host when using sqlserver. 
	user: process.env.DB_USER,
	port: process.env.DB_PORT,
	database: process.env.DB_NAME,
	password: process.env.KMS_DB_PASSWORD
});
```

Example **package.json**: (replace the name with your connector type)
```json
{
	"name": "mysqlConnector",
	"version": "1.0.0",
	"description": "MySQL connector",
	"main": "index.js",
	"directories": {
		"test": "test"
	},
	"scripts": {
		"test": "leo-cli test . "
	},
	"config": {
		"leo": {
		"type": "bot",
			"memory": 256,
			"timeout": 300,
			"role": "ApiRole"
		}
	}
}
```

If you are using a VPC for access to your database, or are using an AWS RDS instance, add the VpcConfig directly below "config" (replace the id's with those from your VPC):
```json
    "VpcConfig": {
        "SecurityGroupIds": [
            "sg-123456fb"
        ],
        "SubnetIds": [
            "subnet-abc12345",
            "subnet-def67890",
            "subnet-ghi45679"
        ]
    }
```

### Step 2: Create a slave database connector.
This will be your data warehouse or anything you want to compare against the master database.
**Repeat step 1** for this bot but with the slave database connection information.
If your slave is not a database but an endpoint, see the custom URL connector section (in-progress).

### Step 3: Deploy the bots
In your service, be sure to install the connector NPM's for the connectors you're using.
#### Available Connectors:
1. leo-connector-mysql:
`npm install leo-connector-mysql`
2. leo-connector-postgres:
`npm install leo-connector-postgres`
3. leo-connector-sqlserver:
`npm install leo-connector-sqlserver`

Now publish and deploy the bots.

Congratulations! You now have connectors setup to run a checksum. Next we'll need to create a checksum runner.

