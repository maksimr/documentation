# Use Aidbox.Cloud

On the [www.health-samurai.io](https://www.health-samurai.io) site, select PRODUCTS/AIDBOX.

![](../.gitbook/assets/scr-2019-02-26_18-15-37.png)

Click the 'TRY IN CLOUD' button.

![](../.gitbook/assets/scr-2019-02-26_18-15-46.png)

Choose how you would like to authoraize [Aidbox](https://www.health-samurai.io/aidbox). It can be done via your Github or Google account.

![](../.gitbook/assets/scr-2019-02-26_18-17-38.png)

If you chose Github authorization, click the 'Authorize HealthSamurai' button.

![](../.gitbook/assets/scr-2018-10-11_10-50-33.png)

Github will ask you to confirm your password to continue.

![](../.gitbook/assets/scr-2018-10-11_10-51-32.png)

And now you are successfully authorized in Aidbox.Cloud. Click the 'New Box' button to start.

![](../.gitbook/assets/scr-2018-10-11_10-51-55.png)

In the displayed form, enter your future box name. It can be a name of your application you are going to build. It will be the base URL of your FHIR server.

![](../.gitbook/assets/scr-2019-02-26_18-29-25.png)

Choose the desired FHIR version, billing method, and click the 'Create' button.

![](../.gitbook/assets/scr-2019-02-26_18-18-49.png)

Your new box will be successfully created. Click the box name to proceed.

![](../.gitbook/assets/scr-2018-10-11_10-54-04.png)

Now you can browse the left navigation menu and work with your box and its database. What you can do here: 

* Settings — view your box settings, base URL, manage your collaborators.
* Resources — view the list of available resources.
* Operations — view the list of available operations.
* Database — work with your database.
* REST console — execute REST requests to your box.
* Auth clients — manage your auth clients.
* Users — manage your box users.
* Access Control — manage access to your box.
* Concepts — view the list of available concepts.
* Documentation — redirects you to the [docs.aidbox.app](https://docs.aidbox.app) site with documentation.
* Support — this is a modal form to send a feedback.

![](../.gitbook/assets/scr-2018-10-11_10-54-09.png)

Let's execute the GET request reading all Patient resources. Access the REST console, enter 'Get /Patient' and press CTRL+Enter. You will see the response.

![](../.gitbook/assets/scr-2018-10-11_10-55-15.png)

