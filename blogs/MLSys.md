# Model Deployment
###	Deployment patterns
	- as an installing package
		Adv:
		- direct access to the model, so execution is fast
		- no need to upload data, as it is on premise
		- works offline
		- vendor has no responsibility.
		Drawbacks:
		- ML code and app code, makes it harder to update the model without upgrading the application.
	- dynamically on users device
		Similar to static deployment, diff. is model is not part of binary code
		Dynamic deployment can be achieved in many ways
			- deploying model parameters
				-the model file only contains the learned parameters, while the user’s device has installed a runtime environment for the model. Some machine learning packages, like TensorFlow, have a lightweight version that can run on mobile devices.
			- serialized object
				- the model file is a serialized object that the application would deserialize. The advantage of this approach is that you don’t need to have a runtime environment for your model on the user’s device. All needed dependencies will be deserialized with the object of the model. An evident drawback is that an update might be quite “heavy,” which is a problem if your software system has millions of users.
			- deploying to the browser	
				- Most modern devices have access to a browser, either desktop or mobile. Some machine learning frameworks, such as TensorFlow.js, have versions that allow to train and run a	model in a browser, by using JavaScript as a runtime.
			Adv:
				- Calls to the model will be fast.
				- only need to serve a webpage.
			Drawbacks:
				- user must always download the model params for every time they start the web app.
				- some users may turn of the updates, at this time, it is difficult to manages the server-side part of the application.
				- code can be revrse engineered as the code is on user device.
				- difficult to monitr the model performance.
			- dynamically on a server
			- on VM
				- Having above complications, we serve the model as REST API service or gRPC.
			- A load balancer dipatches the requests to a VM on its availability
			- Downsides is to maintain servers, network latency, higher cost for VM compared to deployment in a container, or a serverless deployment.
		- in a container.
			docker or kubernetes(unlike VM, it doesnt have multiple OSs.)
		- serverless deployment.
			- The serverless deployment consists of preparing a zip archive with all the code needed to run the machine learning system (model, feature extractor, and scoring code). The zip archive must contain a file with a specific name that contains a specific function, or class-method	definition with a specific signature (an entry point function). The zip archive is uploaded to the cloud platform and registered under a unique name.
			-Adv:
				-cost efficient.
				-simplifies canary deployment.
				-rollbacks are simple
		- via model streaming.
			- Takes single request and does all the necessary functinos flowing through a topology and returns the necessary output to the user.
			- to process one data element, a client using streaming opens a connection, sends a request, and receives update events as they happen.
		
###	Deployment Strategies:
	-Single Deployment
		Single deployment has the advantage of being simple; however, it’s also the riskiest strategy. If the new model or the feature extractor contains a bug, all users will be affected.
	-Silent Deployment
		It deploys the new model version and new feature extractor, and keeps the old ones. Both versions run in parallel.However, the user will not be exposed to the new version until the switch is done. The
		predictions made by the new version are only logged. After some time, they are analyzed to detect possible bugs. Furthermore, for many applications, it’s impossible to evaluate the new model without exposing its predictions to the user.
	-Canary Deployment
		Canarying, pushes the new model version and code to a small fraction of users, while keeping the old version running for most users. Contrary to the silent deployment, canary deployment allows validating the new model’s performance, and its predictions’ effects. Contrary to the single deployment, canary deployment doesn’t affect lots of users in case of possible bugs. By opting for the canary deployment, you accept the additional complexity of having and maintaining several versions of the model deployed simultaneously. An obvious drawback of the canary deployment is that it’s impossible for engineers to spot rare errors. If you deploy the new version to 5% of users, and a bug affects 2% of users, then you have only 0.1% chance that the bug will be discovered.
	-Multi-Armed bandit
		Routed to best performing models, exploration and exploitation.
		
		
###	Automated Deployment, Versioning and Metadata:
	- Model Accompanying Assets
		- an end-to-end set that defines model inputs and outputs that must always work.
		- a confidence test set that correctly defines model inputs and outputs, and is used to compute the value of the metric.
		- a performance metric whose value will be calculated on the confidence test set by applying the model to it.
		- the range of acceptable values of the performance metric.
		- Once the system using the model is initially evoked on an instance of a server or client’s device, an external process must call the model on the end-to-end test data and validate that all predictions are correct. Furthermore, the same external process must validate that the value of the performance metric computed by applying the model to the confidence test set is within the range of acceptable values. If either of two evaluations fails, the model should not be served to the client
	- Version Sync
		versions of these three must be in sync
		- training data
		- feature extractor
		- model
		for every new model, it should go through same versions in the above three, if there is prediciton error in end-to-end test data or the value of the metric is not within the range of acceptable values, entire deployment has to be rolled back.
	- Model version metadata
		- requirements.txt
		- name and version of the model
		- hyperparameters list
		- features required by the model
		- version of the data(train and validation)
	For audit purposes, the following information must also accompany each deployment:
		- who built the model and when,
		- who and when made the decision of deploying that model, and based on what grounds,
		- who reviewed the model for privacy and security compliance purposes.
		
###	Model Deployment best practices.
	- Algorithmic efficiency
	- Deployment of Deep Models only model is deployed on gpus, but downside is communitcion overhead between two parts.
	- Caching: The first time the function run_model is called for some input, model.predict will be called. For the subsequent calls of run_model with the same value of the input, the output will be read from cache that memorizes the result of maxsize most recent calls of model.predict.
	- Delivery format of the model and code
	- Start with a simple model
	- Test on Outsiders.
