# 24rd_SOPT_seminar

- [24rd_SOPT_seminar](#24rd-sopt-seminar)
- [1stWeek](#1stweek)
  * [1. Android](#1-android)
  * [2. Activity](#2-activity)
  * [3. Layout UI](#3-layout-ui)
  * [4. Event Handling](#4-event-handling)
  
- [2ndWeek](#2ndweek)
  * [1. Activities with return values](#1-activities-with-return-values)
  * [2. application local DB](#2-application-local-db)
  * [3. Fragment](#3-fragment)
  * [4. FragmentStatePager](#4-fragmentstatepager)
  
- [3rdWeek](#3rdweek)
  * [1. selector](#1-selector)
  * [2. Glide library](#2-glide-library)
  * [3. RecyclerView](#3-recyclerview)
  
- [4thWeek](#4thweek)
 
# 1stWeek

## 1. Android
**1.1 프로젝트 구조**
- Menifest : 앱에 대한 정보 ex) 앱 이름, 시작 액티비티...

- Activity : 화면 전체를 채우는 하나의 뷰

- Drawable : 앱에서 사용할 리소스를 모아둠 ex) 이미지 자료

- Values : 앱에서 사용하는 값(대명사) 정의 ex) color, string, styles,,

- Build : 외부라이브러리, sdk정보


**1.2 외부 라이브러리**
###### ● 외부라이브러리 등록 방법
gradle>dependency
1) Project Structure>app>dependency>library dependency
2) 직접입력

## 2. Activity
**2.1 Activity란**

**2.2 Activity 생명주기**

**2.3 Activity 전환**

		var intent = Intent(this, LoginActivity::class.java)
		intent.putExtra("id",1)
		startActivity(intent)
		
line 1)  intent의 매개변수(현재 액티비티, 전환 액티비티)

line 2)  보낼때는 자료형에 상관 없이 **putExtra** (단, 받을때는 자료형에 맞는 메소드를 사용해야 함.)


## 3. Layout UI
**3.1 View와 ViewGroup**

**3.2 Attribute**

###### ● padding vs margin
padding : 내부 컨텐츠와의 간격

margin : 다른 뷰와의 간격

**3.3 기타**
###### ● TitleBar 가 없는 Activity
menifest => application태그의 theme 속성 => AppCompat.Light.NoActionBar
###### ● 둥근 모서리 스타일
res/drawable/new_style.xml

속성
solid : 배경 채우기 속성
stroke : 테두리 속성
corners : 모서리 속성

###### ● 앱에서 사용할 값 정의
*자주 사용하는 색 지정
res/color.xml
color태그의 name속성에는 값(대명사), 태그 안에는 색상 코드값

*TitleBar를 없애는 다른방법
res/style.xml
style태그의 parent속성을 "Theme.AppCompat.Light.NoActionBar"

## 4. Event Handling
**4.1 OnFocusChangeListener**

View에 Focus가 잡혔을때 이벤트 처리

        edt_login_id.setOnFocusChangeListener { v, hasfocus ->
            if(hasfocus == true)
                v.setBackgroundResource(R.drawable.primary_border_gray)
            else
                v.setBackgroundResource(R.drawable.primary_border)
        }
line 1) **setOnFocusChangeListener**의 인자(바뀌는 뷰, 활성화가 되었는가(Boolean) ) ->

line 2)  활성화가 되면 테두리가 primary_border_gray style로, 

line 4)  그렇지 않으면 primary_border style로


# 2ndWeek

## 1. Activities with return values
￼
**1.1 startActicity()/finish() -> getExtra()**

** 	   putExtra()**

**1.2 startActivityForResult() -> getExtra()/setResult()/finish() -> onActivityResult()**

LoginActivity.kt

	val REQUEST_CODE_LOGIN_ACTIVITY = 1000

	tv_login_signup.setOnClickListener {

	    val simpleDateFormat = SimpleDateFormat("dd/M/yy hh:mm:ss")
	    val s_time = simpleDateFormat.format(Date())


	    startActivityForResult<SignupActivity>(REQUEST_CODE_LOGIN_ACTIVITY, "start_time" to s_time)
	}
 
startActivityForResult<전환 창>(요청고유번호, SubActivity에 보내고 싶은 데이터명칭 to 데이터벨류)

SignupActivity.kt

	fun postSignupResponse(u_id:String, u_pw: String, u_name: String){

	    val simpleDateFormat = SimpleDateFormat("dd/M/yyyy hh:mm:ss")
	    val e_time = simpleDateFormat.format(Date())

	    val intent = Intent()
	    intent.putExtra("end_time",e_time)
	    setResult(Activity.RESULT_OK, intent)

	    finish()
	}

intent.putExtra
setResult

LoginActivity.kt

	override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
	    super.onActivityResult(requestCode, resultCode, data)

	    if(requestCode == REQUEST_CODE_LOGIN_ACTIVITY){
		if(resultCode == Activity.RESULT_OK) {
		    val e_time = data!!.getStringExtra("end_time")

		    toast("End time: ${e_time}")
		}
	    }
	}
onActivityResult(요청고유번호, 작업처리 결과(RESULT_OK), 부모Activity에 전달하고자 하는 data)

## 2. application local DB

**2.1 Toolbar**
'include' 태그의 layout를 이용하여 외부 layout파일을 불러 올 수 있음!

**2.2 local DB**

*Shared Preference
- Key:Value쌍으로 데이터 저장
- [contezt].getSharedPreference([key],[mode])문법으로 단일 인스턴스에 접근 가능.
- 앱 초기설정(알림/광고메세지수신동의), 편의기능(자동로그인,쿠키)등의 구현에 이용 될 수 있음.

*SQLite

**2.3 Shared Preference**

 1) db>SharedPreferenceController.kt (파일 생성시, 반드시 종류는 Object로!)
 2) SharedPreferenceController.setUserID(this,i_id) 로 메소드 접근
 3) SharedPreferenceController.kt 에 setUserID메소드 생성

*자동로그인 구현하기*

**1-setUserID 메소드로 DB에 아이디 등록**

SharedPreferenceController.kt

	val MY_ACCOUNT = "unique_string"

	fun setUserID(ctx: Context, time:String){
	    val preference : SharedPreferences = ctx.getSharedPreferences(MY_ACCOUNT, Context.MODE_PRIVATE)
	    val editor : SharedPreferences.Editor = preference.edit()

	    editor.putString("u_id",time)
	    editor.commit()
	}

 ctx.getSharedPreferences(MY_ACCOUNT, Context.MODE_PRIVATE//해당 어플리케이션이 아니면 접근 불가능)

**2-getUserDB 메소드로 DB에 아이디가 있는지 확인**

MainActivity.kt

	//로그인or로그아웃 버튼이 눌렸을 때, 수행 해야 할 내용 결정
	txt_toolbar_main_action.setOnClickListener {
	    if(SharedPreferenceController.getUserID(this).isEmpty()){
		//db에 저장된 값이 없다면 (로그아웃 상태 라면)
		startActivity<LoginActivity>()
	    }
	    else{ //로그인 상태 라면
		SharedPreferenceController.clearUserID(this)
		configureTitleBar()
	    }
	}

	//액티비티가 최상단에 띄어 질 때 마다 호출
	override fun onResume() {
	    super.onResume()
	    configureTitleBar()
	}

	private fun configureTitleBar(){
	    if(SharedPreferenceController.getUserID(this).isEmpty()){
		txt_toolbar_main_action.text = "로그인"
	    }
	    else{
		txt_toolbar_main_action.text = "로그아웃"
	    }
	}

SharedPreferenceController.kt

	fun getUserID(ctx : Context):String{
		val preferences:SharedPreferences = ctx.getSharedPreferences(MY_ACCOUNT, Context.MODE_PRIVATE)
		return preferences.getString("u_id","")
	}

**3-clearUserID 메소드로 db에 있는 내용 모두 삭제**

	txt_toolbar_main_action.setOnClickListener {
	    if(SharedPreferenceController.getUserID(this).isEmpty()){
		//db에 저장된 값이 없다면 (로그아웃 상태 라면)
		startActivity<LoginActivity>()
	    }
	    else{ //로그인 상태 라면
		SharedPreferenceController.clearUserID(this)
		configureTitleBar()
	    }
	}

	fun clearUserID(ctx : Context){
	    val preferences : SharedPreferences = ctx.getSharedPreferences(MY_ACCOUNT,Context.MODE_PRIVATE)
	    val editor :SharedPreferences.Editor = preferences.edit()
	    editor.clear()
	    editor.commit()
	}
editor.clear() 는 db의 모든 내용이 지워짐!!!


## 3. Fragment
**3.1 Fragment?**
- 하나의 액티비티가 여러버전의 화면을 가질 수 있음 ex. 모바일환경에서와 태플릿환경에서의 UI
- 한개의 Activity에서 여러개의 UI를 보기 위함
- 재사용 가능한 부분 Activity

**3.2 Fragment의 lifecycle**

- 상위 Activity의 생명주기에 영향을 받음
- Fragment위에 다른 Fragment 올라갈 수 있음
- Activity간에는 intent로 데이터를 전달하지만, Fagment간에는 Bundle로 데이터 전달
- View가 완전히 생성 된 후, 호출되는 Fragment의 생명주기 : onActivityCreated()
￼

## 4. FragmentStatePager

**4.1 언제 FragmentStatePager를 사용 할 까?**

- TabLayout과 함께 사용하여 실용적인 레이아웃 구성!
- 매번 Fragment를 생성하여 보여주는 것이 아니라, 데이터를 보존해 놓음 -> 불필요한 서버 통신 방지 할 수 있음.

**4.2 FragmentStatePager 구현 방법**

	1) ViewPager와 TabLayout 레이아웃 구성
	2) TabLayout과 ViewPager연결 
	3) FragmentStatePagerAdapter생성
	4) ViewPager와 FragmentStatePagerAdapter연결

##### 1) ViewPager와 TabLayout 레이아웃 구성

- TabLayout은 반드시 TabLayout이 아니라, android.support.design.widget.TabLayout 태그 이용할 것 !
- design library등록!

##### 2) TabLayout과 ViewPager연결

:TabLayout을 클릭하면, ViewPager에서 보이는 Fragment를 변화시킬 것 이라는 뜻!


##### 3) FragmentStatePagerAdapter생성

:첫번째 Fragment는 AllProductFragment, 두번째 Fragment는 NewProductFragment…로 지정 한다는 뜻!

adapter>FragmentMainPagerAdapter.kt

	class ProductMainPagerAdapter(fm: FragmentManager?, private val fragment_num : Int): FragmentStatePagerAdapter(fm) {

	    override fun getItem(p0: Int): Fragment? { //?는 널값을 리턴하기도한다는 뜻
		return when (p0){ //반환 프래그먼트 정해 줌
		    0->AllProductMainFragment()
		    1->NewProductMainFragment()
		    2->EndProductMainFragment()

		    else -> null
		}
	    }

	    override fun getCount(): Int {
		return fragment_num
	    }
	}

##### 4) ViewPager와 FragmentStatePagerAdapter연결


MainActivity.kt

	private fun configureMainTab(){
	    vp_main_product.adapter = ProductMainPagerAdapter(supportFragmentManager, 3)
		//ViewPager와 FragmentStatePagerAdapter 연결. viewpager는 adapter가 요구됨! 
	    vp_main_product.offscreenPageLimit = 2
	    tl_main_category.setupWithViewPager(vp_main_product)
		//TabLayout과 ViewPager연결.

		//TabLayout과 navigation_category_main.xml연결
	    val navCategoryMainLayout : View = (this.getSystemService(android.content.Context.LAYOUT_INFLATER_SERVICE) as LayoutInflater)
		.inflate(R.layout.navigation_category_main, null, false)
	    tl_main_category.getTabAt(0)!!.customView = navCategoryMainLayout.findViewById(R.id.rl_nav_category_main_all) as RelativeLayout
	    tl_main_category.getTabAt(1)!!.customView = navCategoryMainLayout.findViewById(R.id.rl_nav_category_main_new) as RelativeLayout
	    tl_main_category.getTabAt(2)!!.customView = navCategoryMainLayout.findViewById(R.id.rl_nav_category_main_end) as RelativeLayout

	}

# 3rdWeek

## 1. selector

**2-1 selector?**

: 상태에 따라(선택이 되었을 때/해지 되었을 때) 화면에 띄울 리소스를 다르게 설정
: 좋아요, 체크박스 등 구현시 사용

**2-2 selector**

	1) res>drawable>***_selector.xml : selector drawable 만들기
	2) xml파일에서, view의 src를 selector drawable로 지정
	3) 코틀린파일에서, view가 클릭될 때, isSelected의 속성 값 변경

##### 1) res>drawable>*_selector.xml : selector drawable 만들기	

action_selector.xml

	<selector>
	    <item
        	android:state_selected="false"
            	android:drawable="@drawable/action_login"/>

    	    <item
        	android:state_selected="true"
        	android:drawable="@drawable/action_logout"/>

	</selector>

##### 2) xml파일에서, view의 src를 selector drawable로 지정


	<ImageView
			android:id="@+id/img_toolbar_main_action"
			android:layout_width="20dp"
			   android:layout_height="20dp"
			android:layout_centerVertical="true"
			android:layout_alignParentRight="true"
			android:layout_marginRight="10dp"
			android:src="@drawable/action_selector"
		/>

##### 3) 코틀린파일에서, view가 클릭될 때, isSelected의 속성 값 변경

    private fun configureTitleBar(){
        if(SharedPreferenceController.getUserID(this).isEmpty()){
            img_toolbar_main_action.isSelected = false
        }
        else{
            img_toolbar_main_action.isSelected = true
        }
	
**2-3 셀렉터로 DotIndicator 구성하기**
	
	1) res>drawable>dot_selector.xml : selector drawable 만들기
	2) xml파일에서, TabLayout view의 tabBackground 속성을 selector drawable로 지정
	3) kotlin파일에서, 뷰페이져와 tablayout 연결
	
##### 3) kotlin파일에서, 뷰페이져와 tablayout 연결

        vp_main_slider.adapter = SliderMainPagerAdapter(supportFragmentManager,3)
        vp_main_slider.offscreenPageLimit = 2

        tl_main_indicator.setupWithViewPager(vp_main_slider)

## 2. Glide library
**1-2 Glide? **

: URI로 이미지를 로딩할 때 사용되는 라이브러리 중 하나.

**1-2 setting using Glide library**

	1) gralde에 라이브러리 추가 (https://github.com/bumptech/glide)
	2) 메니페스트에 어플리케이션 Permission 지정, 
	   HTTPS + HTTP URL 에 접근 할 수 있도록 usesCleartextTraffic 속성 true설정
	3) 이미지에 로드
	
##### 1) gralde에 라이브러리 추가 (https://github.com/bumptech/glide)

##### 2) 메니페스트에 어플리케이션 Permission 지정, HTTPS + HTTP URL 에 접근 할 수 있도록 usesCleartextTraffic 속성 true설정

	    <uses-permission android:name="android.permission.INTERNET"/>
    	    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
	    
##### 3) 이미지 로드

	Glide.with(this)
	.load("이미지 URL")
	.into(이미지를 띄울 이미지 뷰)

## 3. RecyclerView

**3-1 RecyclerView?**

: list형태의 반복되는 레이아웃을 구성하는 방법

**3-2 RecyclerView**
	
	1) Project Structure> Dependency> recycle 입력하여 라이브러리 추가
	2) 해당 activity/Fragment에 RecyclerView layout 추가
	
	**중요**
	3) Data Class 만들기
	4) item layout 만들기
	5) RecyclerView Adapter> view Holder 만들기 ( xml 뷰를 변수에 저장 )
	6) RecyclerView Adapter 완성 (view와 item연결)
	7) kotlin 파일에서, (datalist생성 후) 어뎁터를 RecyclerView와 연결
	
##### 1) Project Structure> Dependency> recycle 입력하여 라이브러리 추가
##### 2) 해당 activity/Fragment에 RecyclerView layout 추가
	
##### 3) Data Class 만들기
##### 4) item layout 만들기
##### 5) RecyclerView Adapter> view Holder 만들기

:recyclerview Adapter에 inner class로 Holder만들기. recyclerView item의 각각의 뷰의 id를 변수에 저장한다.

    inner class Holder(itemView : View) : RecyclerView.ViewHolder(itemView){
        var container = itemView.findViewById(R.id.rl_rv_item_episode_overview_container) as RelativeLayout
        var img_thumbnail = itemView.findViewById(R.id.img_rv_item_episode_overview_thumbnail) as ImageView
        var title = itemView.findViewById(R.id.tv_rv_item_episode_title) as TextView
        var upload_date = itemView.findViewById(R.id.tv_rv_item_episode_upload_date) as TextView
        var views = itemView.findViewById(R.id.tv_rv_item_episode_views)as TextView
    }

##### 6) RecyclerView Adapter 완성 (view와 item연결)


	class EpisodeOverviewRecyclerViewAdapter(val ctx : Context, val dataList :ArrayList<EpisodeOverviewData>): RecyclerView.Adapter<EpisodeOverviewRecyclerViewAdapter.Holder>(){

	    override fun onCreateViewHolder(viewGroup: ViewGroup, viewType: Int) :Holder{
		val view : View = LayoutInflater.from(ctx).inflate(R.layout.rv_item_episode_overview,viewGroup,false)
		return Holder(view)
	    }
	    override fun getItemCount():Int = dataList.size

	    override fun onBindViewHolder(holder: Holder, position: Int) {
		Glide.with(ctx)
		    .load(dataList[position].img_url)
		    .into(holder.img_thumbnail)
		holder.title.text = dataList[position].title
		holder.upload_date.text = dataList[position].upload_date
		holder.views.text = "조회수 "+dataList[position].views.toString() +"회"
	    }

	    inner class Holder(itemView : View) : RecyclerView.ViewHolder(itemView){
		var container = itemView.findViewById(R.id.rl_rv_item_episode_overview_container) as RelativeLayout
		var img_thumbnail = itemView.findViewById(R.id.img_rv_item_episode_overview_thumbnail) as ImageView
		var title = itemView.findViewById(R.id.tv_rv_item_episode_title) as TextView
		var upload_date = itemView.findViewById(R.id.tv_rv_item_episode_upload_date) as TextView
		var views = itemView.findViewById(R.id.tv_rv_item_episode_views)as TextView
	    }
	}

##### 7) kotlin 파일에서, (datalist생성 후) 어뎁터를 RecyclerView와 연결

        episodeOverviewRecyclerViewAdapter = EpisodeOverviewRecyclerViewAdapter(this,dataList)
        rv_episode_overview_list.adapter = episodeOverviewRecyclerViewAdapter
        rv_episode_overview_list.layoutManager = LinearLayoutManager(this,LinearLayout.VERTICAL,false)

**3-3 layoutManager**

# 4thWeek

## 4.setting

**4.1. 라이브러리 설치**

	    implementation 'com.google.code.gson:gson:2.8.5' //gson
	    implementation 'com.squareup.retrofit2:retrofit:2.5.0' //retrofit
	    implementation 'com.squareup.retrofit2:converter-gson:2.5.0' //retrofit2

**4.2. 네트워크 인터페이스 설정**

networt패키지>NetworkService(interface)

		networkSercvice.kt
		interface NetworkService{
		}

**4.3. 어플리케이션 클래스 설정**

- Application Class : Android Conponent들 사이에서 공유 가능한 전역 클래스
- 앱이 실행 될 때 가장 먼저 실행. -> 초기화 할 전역 객체가 있다면 이 곳 에서 하면 됨! ex. NetworkService

		1) Application()을 상속받는 클래스 생성
		2) AndroidManifest.xml에 등록
		3) NetworkService 인터페이스 초기화

.	3.1 Network패키지> ApplicationController
	
		ApplicationController.kt

		import android.app.Application
		class ApplicationController: Application(){
		}

.	3.2 메니페스트에 등록
<application>의 속성에
android:name = ".Network.ApplicationController" 등록

.	3.3 NetworkService 인터페이스(전역객체) 초기화

	class ApplicationController : Application(){
		private val baseURL = "http://hyunjkluz.ml:2424/" //통신하고자 하는 API 서버의 기본주소
		lateinit val networkService : NetworkService //선언만 먼저!
		
		companion object{
			lateinit var instance : ApplicationController
		}
		
		override fun onCreate(){
			super.onCreate()
			instance = this
			buildNetwork()
		}
		
		//Retrofit 객체 생성
		fun buildNetwork(){
			//Retrofit 객체 생성
			val retrofit:Retrofit = Retrofit.Builder()
				.baseUrl(baseURL)
				.addConverterFactory(GsonConveterFactory.create())
				.build()
				
			networkService = retfit.create(NetworkService::class.java) //Retrofit 객체 활성화
		}
	}

## 2.POST Method

**2.1 HTTP Response의 BodyData를 담을 data class만들기**

		data class PostLoginResponse(
			val status : Int,	//상태코드
			val success : Boolean,	
			val message: String,	
			val data : String? 	//?:Null값을 가질 수 도 있음
		)
**2.2 NetworkInterface 통신을 담당하는 추상 메소드 만들기**
		
		interface NetworkInterface{
		
			//@HTTP메소드(API URL)
			@POST("/api/auth/signin")
			fun postLoninResponse(
				@Header("Content-Type") content_type : String, //HTTP Request Header
				@body() Body:JsonObject	//보내는 데이터 타입
			): Call<PostLoginResponse> //<HTTP Response 포멧>
		}
**2.3 Login.kt 에서 HTTP Response에 따른 기능 구현**

NetworkInterface 객체 불러오기

		val networkService : NetworkService by lazy{
			ApplicationController.instance.networkService
		}
		
		override fun onCreate(savedInstanceState : Bundle?){
		
		/*생략*/
		
			btnLoginSubmit.setOnClickListener{
				/*생략*/
				if(isVaild(login_u_id, login_u_pw))
					postLoginResponse(login_u_id, login_u_pw)
			}
		}
**2.4 postLoginResponse함수 구현**
		
		fun postLoginResponse(u_id:String,u_pw:String){
		
			var jsonObject = JSONObject()
			jsonObject.put("id", u_id)
			jsonObject.put("password", p_pw)
			//request시 전송 할 데이터를 json객체로 만듦
			
			val gsonObject =JsonParser().parse(jsonObject.toString()) as JsonObject //json객체를 gson객체로
			val postLoginResponse : Call<PostLoginResponse> = networkService.postLoginResponse("application/json", gsonObject)
			//실제 통신 요청("Header", body)
			
			postLoginResponse.enqueue(object : Callback<PostLoginResponse>{
				
				//HTTP Response를 받지 못했을 때,
				override fun onFailure(call : Call<PostLoginResponse>, t:Throwable){
					Log.e("Login failed", t.toString())
				}
			})
				//HTTP Response를 받았을 때,
				override fun onResponse(call:Call<PostLoginResponse>, response:Response<PostLoginResponse>){
				
				if(response.isSuccessful){
					if(response.body()!!.status ==201){
						SharedPreferenceController.setUserToken(applicationContext, response.body!!.data)
			finish()
					}
				}
			}
		}
		
**2.5 SharedPreference 관리**

		fun setUserToken(ctx:Context, time:String){
			val preference : SharedPreferences = ctx.getSharedPreference
			/**/
		}
## 3. GET Method

**3.1. NetworkService에 API 처리 함수 작성**

#Query사용시, (Key = Value)
api/webtoons/main?flag=1

		@GET("/api/webtoons/main")
		fun getMainProductListResponse(
			@Header("Content-Type") content_type:String,
			@Query("flag") flag:Int
		): Call <GetMainProductListResponse>
	
#Path사용시,
api/webtoons/main/1

		@GET("/api/webtoons/main/{main}")
		fun getMainProductListResponse(
			@Header("Content-Type") content_type:String,
			@Path("flag") flag:Int
		): Call <GetMainProductListResponse>
**3.2 HTTP Response의 BodyData를 담을 data class만들기**

		data class GetMainProductListResponse(
			val status:Int,
			val success : Boolean,
			val message : String,
			val data : ArrayList<ProductOverviewData>
		)
		
**3.3 ProductOverviewData.kt**	

받아온 데이터가 리스트는 형태 일 경우 리스트의 데이터와 같은 내용을 포함하고 있어야 함

		data class ProductOverviewData(
			var thumnail : String,
			var idx: Int,
			var title : String,
			var likes:Int,
			var isFinished:Int
		)
**3.4 AllProductMainFragment.kt에서 HTTP Response에 따른 기능 구현**


		private fun getMainProductResponse(){
			val getMainProductListResponse = networkService.getMainProductListResponse("application/json",1) //실제 통신 요청
			getMainProductListResponse.enqueue(object: Callback<GetMainProductListRespnose>{
				//실패 할 
				override fun onFailure(call<GetMainProductListResponse>, t:Throwable){
				Log.e("AllMainProduct List Fail", t.toString())
				}
				
				override fun onResponse(
					call: Call<GetMainProductListResponse>,
					response:Response<GetMainProductListResponse>
				){
					//성공 할 경우
					if(response.isSuccessful){
						if(response.body()!!.status == 200){
							val tmp:ArrayList<ProductOverview> = respnose.body()!!.data!!
							productOverviewRecyclerViewAdapter.dataList = tmp //dataList업데이트
							productOverviewRecyclerViewAdapter.notifyDataSetChanged() //어뎁터에 데이터가 변경 될 사실을 알려주어야 함!
						}
					}
				
				}
			})
		
		}

## 4 Multipart
