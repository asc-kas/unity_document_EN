# ASCSDK Unity Document

------
###Introduction
Welcome to ASCSSDK. ASCSSDK is an advertising integration SDk that allows you to quickly integrate advertising
###Directory
> * ASCSDK access
> * ASCSDK The working process
> * AD sample
> * Payment and other examples
> * Statistics
> * Build
### ASCSDK Access
   First download the latest Unity SDK
   
   After downloading, create a new Unity project or simply double-click to import the ASCSDK resources
> *Development environment requirements：Unity5.5+。Unity  java

###  AD Sample

> * No need to load multiple ads
##### Banner

    void Start()
    {
        //initialize
        ASCSDKInterface.Instance.Init();
    }
    
    //Display or Hide banner
    void OnBannerClick(bool isOn)
    {
       if (isOn)
           ASCSDKInterface.Instance.ShowBanner();  //show ads
       else
           ASCSDKInterface.Instance.HideBanner();//Hide ads
    }
    

> * Ads that require multiple loads: screenshots
##### Inters 

    void Start()
    {
        //initialize
        ASCSDKInterface.Instance.Init();
    }
    
    //Display Inters
    void OnIntersClick(string level)
    {
        //The advertisement is already available
        if (ASCSDKInterface.Instance.GetIntersFlag())
        {
            //Pass in the current level and if not, pass in "0"
            ASCSDKInterface.Instance.ShowInters(level);
        }
    }
    


> * Ads that require multiple loads and callbacks
##### Video 

    void Start()
    {
        //initialize
        ASCSDKInterface.Instance.Init();
        ASCSDKInterface.Instance.OnVideoCallBack = delegate(CallBackStatus status)
        {
              if (status==CallBackStatus.SUCCEED)
              {
                  //Succeed
              }else
              {
                  //Defeated
              }
        }；
    }
    
    //Display Video
    void OnVideoClick()
    {
        if (ASCSDKInterface.Instance.GetVideoFlag())
        {
            ASCSDKInterface.Instance.ShowVideo();
        } 
    }

 
> * PS: set the horizontal screen manually according to the requirements of the game project: Assect-> plugins-> android-> androidmanifest.xml file

     android:screenOrientation="portrait"

### Pay and other examples
> *Login, logout, upload data
##### Ogin、LogOut、Upload Data

    void Start()
    {
        //initialize
        ASCSDKInterface.Instance.Init();
         ASCSDKInterface.Instance.OnLoginSuc = delegate (ASCLoginResult result)
        {
            OnLoginSuc(result);
        };
        ASCSDKInterface.Instance.OnLogout = delegate ()
        {
            OnLogout();
        };
        //Upload game data
        SubmitGameDataAfferent(ASCExtraGameData.TYPE_ENTER_GAME);
    }
    
    //upload data
    void SubmitGameDataAfferent(int type)
    {
        ASCExtraGameData AfferentData = new ASCExtraGameData();
        //Call time, the value corresponding to type is detailed in the comments of ASCExtraGameData
        AfferentData.dataType = type;
        //Character ID
        AfferentData.roleID = "role01";
        //Character name
        AfferentData.roleName = "role01";
        //Character level
        AfferentData.roleLevel = "10";
        //Server ID
        AfferentData.serverID = 1;
        //Server Name
        AfferentData.serverName = "asc";
        //The current role generates the number of virtual COINS owned
        AfferentData.moneyNum = 1;
        //The current role generates the number of virtual COINS owned
        AfferentData.roleCreateTime = DateTime.Now.Second.ToString();
        //Character creation time, the time from 1970 to now, per second
        AfferentData.roleLevelUpTime = DateTime.Now.Second.ToString();
        //Upload game data
        ASCSDKInterface.Instance.SubmitGameData(AfferentData);
    }
    
     void Update()
        {
            //exit（*Call when pressed）
            if (Input.GetKeyUp(KeyCode.Escape))
            {
                 //Call the exit confirmation box of the channel, return false, the SDK does not support, the game needs to use its own exit box
    			if (ASCSDKInterface.Instance.IsSupportExit())
    			{
    				ASCSDKInterface.Instance.SDKExit();
    			}
    			else
    			{
    				//Call your exit interface
    			}
    			SubmitGameDataAfferent(ASCExtraGameData.TYPE_EXIT_GAME);
            }
        }

    //In the callback
    void OnLoginSuc(ASCLoginResult result)
    {
        if (!result.isSuc)
        {
            //login failure
            return;
        }
        if (result.isSwitchAccount)
        {
            //"Switch account successful:" + result.token;//Switch account successful
        }
        else
        {
            //"login successfully:" + result.token;//login successfully
        }
    }
    
    //Logout callback
    void OnLogout()
    {
         //Have to log out
    }

> * Pay
##### Pay

    void Start()
    {
        //initialize
        ASCSDKInterface.Instance.Init();
        //payment callback
        ASCSDKInterface.Instance.OnPayCallBack = delegate (CallBackStatus status,int productId)
        {
            OnLPaySuc(status，productId);
        };
        //Upload game data(must)
        SubmitGameDataAfferent(ASCExtraGameData.TYPE_ENTER_GAME);
    }
    
    //pay callback
    void OnLPaySuc(CallBackStatus status,int productId)
    {
        if(status==CallBackStatus.SUCCEED)
        {
            //pay success
        }
        else
        {
            //Payment Failed
        }
    }
    
   	/// <summary>
	/// Pay
	/// </summary>
	/// <param name="Price">Price</param>
	/// <param name="productId">Payment id for callback</param>
	/// <param name="notice">（Notice</param>
    void OnPayClick(int Price,int productId,string notice)
    {
        ASCPayParams data = new ASCPayParams();
        //In-game product ID (must)
        data.productId = productId.ToString();
        //The name of goods in the game, such as ingots, diamonds...(must)
        data.productName = "wing";
        //Product description (must)
        data.productDesc = notice;
        //Price, in yuan
        data.price = Price;
        // purchase quantity, set as 1 (must)
        data.buyNum = 1;
        //The amount of virtual currency left over from the current player
        data.coinNum = 300;
        //The server ID where the current role is located
        data.serverId = "10";
        //The name of the server where the current role is located
        data.serverName = "地狱之恋";
        //Current role ID
        data.roleId = "asc_24532452";
        //Current role name
        data.roleName = "麻利麻利吼";
        //Current role level
        data.roleLevel = 15;
        //The payment callback address of the game server is used to receive payment callback notification
        data.payNotifyUrl = "";
        //The current role's VIP rating
        data.vip = "v15";
        //Extending the data, when a successful callback is paid to notify the game server, the value is returned intact
        data.extension="";
        ASCSDKInterface.Instance.Pay(data);
    }
> * Share and evaluate and Gift bag for
##### Share、Evaluate、Gift bag for

    void Start()
    {
        //initialize
        ASCSDKInterface.Instance.Init();
        //Upload game data
        SubmitGameDataAfferent(ASCExtraGameData.TYPE_ENTER_GAME);
        //Gift bag for callBack
        ASCSDKInterface.Instance.OnGetGiftInfo = OnGetGiftInfo;
    }
    
    //Share
    void Share()
    {
        ShareParams SP = new ShareParams();
        //Share the title, up to 30 characters
        SP.title = "Share the title";
        //The title link
        SP.titleUrl = "http://www.uustory.com/";
        //Share the source name that this content shows
        SP.sourceName = "The source name";
        //The source link
        SP.sourceUrl = "http://www.uustory.com/";
        //Content, up to 130 characters
        SP.content = "Share content";
        //Link, WeChat will be used when sharing
        SP.url = "http://www.uustory.com/";
        //Picture address
        SP.imgUrl = "http://www.uustory.com/";
        //Full screen or dialog box
        SP.dialogMode = true;
        //The Notification icon
        SP.notifyIcon = 1;
        //Notification of the text
        SP.notifyIconText = "text";
        //Content comments, which must be Shared by renren, cannot be empty
        SP.comment = "Comment, renren share must parameters, cannot be empty";
        ASCSDKInterface.Instance.Share(SP);
    }
    
    //evaluate
    void OnEvaluationClick()
    {
		//Allows the evaluation of
		if (ASCSDKInterface.Instance.GetRateFlag())
		{
			Show your five-star praise interface, and call OnEvaluation() if the player confirms the evaluation.
		}
    }
    
    //Skip to the channel evaluation interface
    void OnEvaluation()
    {
		//In the interface, the user clicks ok to call this method
		ASCSDKInterface.Instance.Evaluation();
    }
    
    //Cash gift bag，If cdkey is 0, the input interface of the SDK is called
    void OnGetGiftInfoClick(string cdkey)
    {
        //cdkey is Exchange codes for gift bags,It's usually handed out in the background。
        ASCSDKInterface.Instance.ExchangeGift(cdkey);
    }
    
    //Gift bag for
    void OnGetGiftInfo(int propNumber, string msg, string type)
    {
        //PropNumber is the number of props sent down, MSG is the status information (if it is 0, the prop sent down fails), and type is the prop type (Golds, Diamonds, Or other customizations).
    }


###  Statistics

    //Starting level
    ASCSDKInterface.Instance.StartLevel(string num);

    //Victory gate
    ASCSDKInterface.Instance.FinishLevel(string num);

    //Failure levels
    ASCSDKInterface.Instance.FailLevel(string num);

    //Item: property name, num: consumption amount, price: needed gold COINS
    ASCSDKInterface.Instance.UserUseBoosterInfo(string item, int num, double price);

    //Item: item name, num: number of items purchased, price: needed gold COINS
    ASCSDKInterface.Instance.UserBuyBoosterInfo(string item, int num, double price);

    //Custom event eventName: custom eventName (implemented by the other party)
    ASCSDKInterface.Instance.UserCustomEvent(string eventName);
    
    //Upload user game parameters, level-> players currently pass the highest level of clearance, the total number of buff > players' current props, and the number of coin-> players' current remaining gold COINS
	ASCSDKInterface.Instance.UploadUserInfo(string level, string buff, string coin);

### Matters needing attention
If you are using a third-party plug-in, make sure your project folder is in the Assect-> plugins-> androidb2 androidmanifest.xml file
Under the application contains the android: name = "com. Asc. SDK. ASCApplication" >

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" 
        android:name="com.asc.sdk.ASCApplication">


###  Build
Package Android applications, switch to Android platform in build Settings, build。After successful packaging, video advertising, payment callback, exit and gift exchange will be called before access to channel resources for testing.


------



火星人互动娱乐有限公司 [Martians]
August 21, 2018





