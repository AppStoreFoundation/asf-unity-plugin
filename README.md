# ASF AppCoins Unity Plugin

![picture](Screenshots/logos.png)

This is the official Unity plugin for the ASF AppCoins Protocol that allows you to integrate ASF AppCoins In-App Billing or Proof-of-Attention Ads into your Unity Android game.

## About ASF AppCoins Unity Plugin
This plugin is developed from a fork of the unofficial unity plugin for AppCoins by [codeberg-io](https://github.com/codeberg-io/AppcoinsUnityPlugin).
 We thought it was a great initiative and decided to support the project and help all Unity developers who would be integrating the ASF AppCoins In-App Billing into their game.

## Integrating the plugin into your game

1. Download the [plugin package file](https://github.com/AppStoreFoundation/asf-unity-plugin/releases) regarding your current Unity version. Open the package in your Unity project (double click the file or in Unity go to Assets -> Import Package -> Custom Package.... and find the file you just downloaded). If you don't want to import the 'AppcoinsUnity/Example' and the 'Resources' folders, make sure to untick them. Everything else is mandatory.

![picture](Screenshots/shot8.png)

2. From the Assets -> AppcoinsUnity -> Prefabs folder drag and drop the _ASFAppcoinsUnity_ prefab into your scene or hierarchy window. If you want you can change the name of _ASFAppcoinsUnity_ gameobject.

![picture](Screenshots/shot15.png)

3. In the inspector window where you have _Receiving Address_, change the text to your ASF wallet address where you would like to receive your AppCoins.

4. Check the _enable debug_ checkbox if you would like to be able to use testnets like Ropsten for testing your AppCoins In-App Billing integration.
**Note: Uncheck this in production to avoid testnet purchases.**

5. Create _Purchaser_ class in Unity C# by inheriting from the _AppcoinsPurchaser_ class. When the _Purchaser_ class wants to buy a product it has to call _makePurchase_ method, with the SKU id of the product to buy, implemented by _AppcoinsPurchaser_. After the purchase has been processed one of the two methods (_purchaseSuccess_, _purchaseFailed_) will be called by _AppcoinsPurchaser_. Those two methods are virtual and empty so you can override them and implement a custom version:

```
using UnityEngine;
using UnityEngine.UI;

//add this namespace to your script to give you  access to the plugin classes.
using Aptoide.AppcoinsUnity;

//Inherit from the AppcoinsPurchaser Class
public class Purchaser : AppcoinsPurchaser {

	public Text message;

	void Start()
	{
		message.text = "Welcome to cody snacks shop!";
	}

	public override void PurchaseSuccess (string skuid)
	{
		base.PurchaseSuccess (skuid);
		//purchase is successful release the product

		if(skuid.Equals("dodo"))
		{
		message.text="Thanks! You bought dodo";
		}

		else if(skuid.Equals("monster"))
		{
		message.text="Thanks! You bought monster drink";
		}

		else if(skuid.Equals("chocolate"))
		{
			message.text="Thanks! You bought chocolate";
		}
	}

	public override void PurchaseFailure (string skuid)
	{
		base.PurchaseFailure (skuid);
		//purchase failed perhaps show some error message

		if(skuid.Equals("dodo"))
		{
			message.text="Sorry! Purchase failed for dodo";
		}

		else if(skuid.Equals("monster"))
		{
			message.text="Sorry! Purchase failed for drink";
		}

		else if(skuid.Equals("chocolate"))
		{
			message.text="Sorry! Purchase failed for chocolate";
		}
	}
	//methods starts the purchase flow when you click their respective buttons to purchase snacks
	public void buyDodo(){
    // MakePurchase receives the sku id of the product to buy
		MakePurchase("dodo");
	}

	public void buyMonster(){
		MakePurchase("monster");
	}

	public void buyChocolate(){
		MakePurchase("chocolate");
	}
}

```
6. _RegisterSKUs_ method has to be overridden by your _Purchaser_ class to register all the SKUs you want. This method will be called by _AppcoinsUnity_ To register a _SKU_ you can call the method _AddSKU_, that receives a _AppcoinsSKU_ object, already implemented at _AppcoinsPurchaser_). This object takes 3 parameters a string with the product name, a string with the product SKU and a float with the price in APPC.


```

	public override void RegisterSKUs()
	{
    //                      Name,       sku id,    price
		AddSKU(new AppcoinsSKU("Chocolate", "chocolate", 0.1));
		AddSKU(new AppcoinsSKU("Monster Drink", "monster", 0.1));
		AddSKU(new AppcoinsSKU("Dodo", "dodo", 0.1));
	}


```

7. Create an object in your scene and add the purchaser script you created to it. Drag and drop the purchaser object to the slot where you have the _Purchaser Object_ on the _AppcoinsUnity_ prefab you added to your scene earlier.

![picture](Screenshots/shot16.png)

## To build the project

Go to the build menu (File -> Build Settings)
1. Check that the build system is set to "Gradle"
(if the import was done correctly this should've changed automatically).

![picture](Screenshots/buildSystemGradle.png)

Now click _Player Settings_.

On the _Player Settings_ window:
1. Click the other settings panel

2. Make sure you change the package name to your liking (if it was Unity's default one now it changed to com.aptoide.appcoins).

3. Make sure that you have min sdk version set to 21 (if the import was done correctly this should've changed automatically).

**Unity 2018.2b (and above)**

1. Connect the phone to your machine and click _Build and Run_

You should have your game running on the phone!

**Unity 5.6 (and above)**

1. Close the _Player Settings_ window

2. On the top bar click _AppCoins_

3. Click _Custom Android Build_

4. This popup will show up

![picture](Screenshots/shot12.png)

5. The gradle path should be picked from the path to your Android Studio installation

6. The adb path will be picked automatically (assuming you have Android SDK path specified in Unity)

7. Pick the scenes you want to include. The ones added to the build settings will automatically be selected for you

8. When you click _Confirm_ a pop up will show up asking you to pick a folder to generate the _Android_ project to. Pick a folder of your liking preferably inside the project root (it can't be the project root itself).

**NOTE:** The final build will be located here:
  FolderYouChoseToBuildTo/ProjectName/build/outputs/apk/
  in a subfolder called debug or release, depending on build settings)

9. When you pick the folder the build process will start. The normal build process will happen and then the custom build process will kick in opening a terminal window. Unity might seem to be not responding but not worry! This is normal because it's waiting for the terminal processes to finish.

10. If you ticked _Install build when done?_ make sure you have your phone connected to the computer and that you unlock it to allow ADB to run

  a. If you also ticked _Run build when done?_ after the installation the game will be launched automatically.

![picture](Screenshots/shot13.png)

11. The build process completed. You can run the app on your phone!

**NOTE:** Although the process screenshots show MacOS this process was tested and successfully ran on Windows and Linux as well!

## To make a signed build
1. Go to Edit -> Project Settings -> Player

2. Open the Publishing Settings tab

If you already have a keystore:
3. Tick "Use Existing Keystore" and then click "Browse keystore" to fetch it.

4. You have to provide the keystore password to allow Unity to read the key aliases.

5. Pick the correct alias and provide it's password as well

 ![picture](Screenshots/keystore.png)

6. You're done!

If you don't have a key already:
3. Tick "Create new keystore..." and then click "Browse Keystore"

4. Now pick the path where the key will be created

5. Now pick a password and write it again to confirm

6. Click the alias dropdown and then chose "Create a new key"

![picture](Screenshots/pickAlias.png)

7. Fill in all the details and click "Create Key"

![picture](Screenshots/createAlias.png)

8. Now go back to the alias dropdown and pick the alias you just created

9. You're done!

## To run the project
To successfully run the project you need to:
1. Download and install ASF Wallet app (you can get it on [Aptoide](https://asf-wallet-app-store-foundation.en.aptoide.com/?store_name=asf-store) or [GooglePlay](https://play.google.com/store/apps/details?id=com.asfoundation.wallet))

![picture](Screenshots/asfIcon.png)

2. Open ASF Wallet and create or restore a wallet
3. Launch the game

## To make sure integration is ok
**Testing POA**

If you enabled PoA, on the _AppCoinsUnity_ object, the expected flow for the app is to show you a notification saying that the PoA started

![picture](Screenshots/poa.png)

**Testing IAB**

To test your purchases, just follow the normal flow you do to trigger them. When they're triggered, this screen should show up:

![picture](https://www.appstorefoundation.org/img/image-howto-donate.gif)

-Make sure you test both the success and failure cases!

**Note: You can test the purchase flow right from the Editor to easily check both flows without having to build for the device**

Trying to make the purchase through the Editor should display a popup like this:

![picture](Screenshots/shot14.png)

Pressing "Test success" will go through with the flow as if the purchase was successful (_purchaseSuccess_ on _Purchaser_ will be called).

Pressing "Test failure" will go through with the flow as if the purchase failed or was canceled (_purchaseFailed_ on _Purchaser_ will be called).

You're DONE! Congrats!
