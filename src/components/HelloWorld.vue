<template>
  <div>
		<div ><h2>空全局创建系统</h2></div>
    <div class="preinfo">
		<div style="margin-bottom:7px;width:100%" >
      <label for="username"><b>账户: </b></label>
      <input type="text" style="width:77%" id="username" placeholder="请输入微软账户" v-model="username" :disabled="prestage"/>
		</div>
		<div style="margin-bottom:7px">
      <label for="password"><b>密码: </b></label>
      <input type="text" style="width:77%" id="password" placeholder="请输入密码" v-model="password" :disabled="prestage" />
		</div>	
		<div style="margin-bottom:7px">
			<b>国家: </b>
			<select id="location" :disabled="prestage" v-model="location" style="height:21px;width:80%">
        <option v-for="(country,index) in countries" :key=index :value="country.countryCode">
            {{country.displayName}}
        </option>
			</select> 
		</div>
    <div style="margin-bottom:7px">
      <b>删除老账户:</b> <input type="checkbox" :disabled="prestage" v-model="isDeleteUser">
    </div>
    </div>
    <div class="startUp">
      <input  type="button" value="开始" stlye="text-align: right" @click="startUp()" :disabled="prestage" />
      &nbsp;
      <input  type="button" value="重置" stlye="text-align: right" @click="reset()" :disabled="prestage" />
    </div>
		<div class="msg1" v-html="msg" :hidden=!isShowTip>
    </div>
    <div class="msg2" :hidden=isShowTip>
      <img :src="'data:image/png;base64,'+challengeString" @click="getChallengeCd()"/><br>
			验证码: <input style='margin-bottom:7px' v-model="inputSolution" /><br>
			<input type='button' value='验证' @click='createTenant()' :disabled=disableVerifyBtn />
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'HelloWorld',
  props: {
    msgInfo: String
  },
  data() {
    return {
      //baseUrl: 'http://127.0.0.1:8010',
      baseUrl: '',
      username: '',
      password: '',
      inputSolution: '',
      challengeString: '',
      token: '',
      challengeId: '',
      azureRegion: '',
      location: 'SG',
      newTenantId: '',
      dirName: '',
      prestage: false,
      isDeleteUser: false,
      isShowTip: true,
      disableVerifyBtn: false,
      msg: '1. 输入正确的账户和密码后点击开始<br>'+
      '2. 输入正确验证码之后点击验证<br>'+
      '3. 耐心等待一段时间即可获得空全局<br><br>'+
      'PS:如果需要在新全局中删除创建它的帐号，可以勾上删除老账户，系统会自动进行异步删除',
      
    countries: [
      {
        "displayName": "Afghanistan",
        "countryCode": "AF"
      },
      {
        "displayName": "Åland Islands",
        "countryCode": "AX"
      },
      {
        "displayName": "Albania",
        "countryCode": "AL"
      },
      {
        "displayName": "Algeria",
        "countryCode": "DZ"
      },
      {
        "displayName": "American Samoa",
        "countryCode": "AS"
      },
      {
        "displayName": "Andorra",
        "countryCode": "AD"
      },
      {
        "displayName": "Angola",
        "countryCode": "AO"
      },
      {
        "displayName": "Anguilla",
        "countryCode": "AI"
      },
      {
        "displayName": "Antarctica",
        "countryCode": "AQ"
      },
      {
        "displayName": "Antigua and Barbuda",
        "countryCode": "AG"
      },
      {
        "displayName": "Argentina",
        "countryCode": "AR"
      },
      {
        "displayName": "Armenia",
        "countryCode": "AM"
      },
      {
        "displayName": "Aruba",
        "countryCode": "AW"
      },
      {
        "displayName": "Australia",
        "countryCode": "AU"
      },
      {
        "displayName": "Austria",
        "countryCode": "AT"
      },
      {
        "displayName": "Azerbaijan",
        "countryCode": "AZ"
      },
      {
        "displayName": "Bahamas, The",
        "countryCode": "BS"
      },
      {
        "displayName": "Bahrain",
        "countryCode": "BH"
      },
      {
        "displayName": "Bangladesh",
        "countryCode": "BD"
      },
      {
        "displayName": "Barbados",
        "countryCode": "BB"
      },
      {
        "displayName": "Belarus",
        "countryCode": "BY"
      },
      {
        "displayName": "Belgium",
        "countryCode": "BE"
      },
      {
        "displayName": "Belize",
        "countryCode": "BZ"
      },
      {
        "displayName": "Benin",
        "countryCode": "BJ"
      },
      {
        "displayName": "Bermuda",
        "countryCode": "BM"
      },
      {
        "displayName": "Bhutan",
        "countryCode": "BT"
      },
      {
        "displayName": "Bolivia",
        "countryCode": "BO"
      },
      {
        "displayName": "Bonaire, Sint Eustatius, and Saba",
        "countryCode": "BQ"
      },
      {
        "displayName": "Bosnia and Herzegovina",
        "countryCode": "BA"
      },
      {
        "displayName": "Botswana",
        "countryCode": "BW"
      },
      {
        "displayName": "Bouvet Island",
        "countryCode": "BV"
      },
      {
        "displayName": "Brazil",
        "countryCode": "BR"
      },
      {
        "displayName": "British Indian Ocean Territory",
        "countryCode": "IO"
      },
      {
        "displayName": "Brunei",
        "countryCode": "BN"
      },
      {
        "displayName": "Bulgaria",
        "countryCode": "BG"
      },
      {
        "displayName": "Burkina Faso",
        "countryCode": "BF"
      },
      {
        "displayName": "Burundi",
        "countryCode": "BI"
      },
      {
        "displayName": "Cabo Verde",
        "countryCode": "CV"
      },
      {
        "displayName": "Cambodia",
        "countryCode": "KH"
      },
      {
        "displayName": "Cameroon",
        "countryCode": "CM"
      },
      {
        "displayName": "Canada",
        "countryCode": "CA"
      },
      {
        "displayName": "Cayman Islands",
        "countryCode": "KY"
      },
      {
        "displayName": "Central African Republic",
        "countryCode": "CF"
      },
      {
        "displayName": "Chad",
        "countryCode": "TD"
      },
      {
        "displayName": "Chile",
        "countryCode": "CL"
      },
      {
        "displayName": "China",
        "countryCode": "CN"
      },
      {
        "displayName": "Christmas Island",
        "countryCode": "CX"
      },
      {
        "displayName": "Colombia",
        "countryCode": "CO"
      },
      {
        "displayName": "Comoros",
        "countryCode": "KM"
      },
      {
        "displayName": "Cook Islands",
        "countryCode": "CK"
      },
      {
        "displayName": "Costa Rica",
        "countryCode": "CR"
      },
      {
        "displayName": "Côte d’Ivoire",
        "countryCode": "CI"
      },
      {
        "displayName": "Croatia",
        "countryCode": "HR"
      },
      {
        "displayName": "Curaçao",
        "countryCode": "CW"
      },
      {
        "displayName": "Cyprus",
        "countryCode": "CY"
      },
      {
        "displayName": "Czech Republic",
        "countryCode": "CZ"
      },
      {
        "displayName": "Democratic Republic of Congo",
        "countryCode": "CD"
      },
      {
        "displayName": "Denmark",
        "countryCode": "DK"
      },
      {
        "displayName": "Djibouti",
        "countryCode": "DJ"
      },
      {
        "displayName": "Dominica",
        "countryCode": "DM"
      },
      {
        "displayName": "Dominican Republic",
        "countryCode": "DO"
      },
      {
        "displayName": "Ecuador",
        "countryCode": "EC"
      },
      {
        "displayName": "Egypt",
        "countryCode": "EG"
      },
      {
        "displayName": "EH",
        "countryCode": "EH"
      },
      {
        "displayName": "El Salvador",
        "countryCode": "SV"
      },
      {
        "displayName": "Equatorial Guinea",
        "countryCode": "GQ"
      },
      {
        "displayName": "Eritrea",
        "countryCode": "ER"
      },
      {
        "displayName": "Estonia",
        "countryCode": "EE"
      },
      {
        "displayName": "Ethiopia",
        "countryCode": "ET"
      },
      {
        "displayName": "Faroe Islands",
        "countryCode": "FO"
      },
      {
        "displayName": "Fiji",
        "countryCode": "FJ"
      },
      {
        "displayName": "Finland",
        "countryCode": "FI"
      },
      {
        "displayName": "France",
        "countryCode": "FR"
      },
      {
        "displayName": "French Guiana",
        "countryCode": "GF"
      },
      {
        "displayName": "French Polynesia",
        "countryCode": "PF"
      },
      {
        "displayName": "Gabon",
        "countryCode": "GA"
      },
      {
        "displayName": "Gambia, The",
        "countryCode": "GM"
      },
      {
        "displayName": "Georgia",
        "countryCode": "GE"
      },
      {
        "displayName": "Germany",
        "countryCode": "DE"
      },
      {
        "displayName": "Ghana",
        "countryCode": "GH"
      },
      {
        "displayName": "Gibraltar",
        "countryCode": "GI"
      },
      {
        "displayName": "Greece",
        "countryCode": "GR"
      },
      {
        "displayName": "Greenland",
        "countryCode": "GL"
      },
      {
        "displayName": "Grenada",
        "countryCode": "GD"
      },
      {
        "displayName": "Guadeloupe",
        "countryCode": "GP"
      },
      {
        "displayName": "Guam",
        "countryCode": "GU"
      },
      {
        "displayName": "Guatemala",
        "countryCode": "GT"
      },
      {
        "displayName": "Guernsey",
        "countryCode": "GG"
      },
      {
        "displayName": "Guinea",
        "countryCode": "GN"
      },
      {
        "displayName": "Guinea-Bissau",
        "countryCode": "GW"
      },
      {
        "displayName": "Guyana",
        "countryCode": "GY"
      },
      {
        "displayName": "Haiti",
        "countryCode": "HT"
      },
      {
        "displayName": "Honduras",
        "countryCode": "HN"
      },
      {
        "displayName": "Hong Kong SAR",
        "countryCode": "HK"
      },
      {
        "displayName": "Hungary",
        "countryCode": "HU"
      },
      {
        "displayName": "Iceland",
        "countryCode": "IS"
      },
      {
        "displayName": "India",
        "countryCode": "IN"
      },
      {
        "displayName": "Indonesia",
        "countryCode": "ID"
      },
      {
        "displayName": "Iraq",
        "countryCode": "IQ"
      },
      {
        "displayName": "Ireland",
        "countryCode": "IE"
      },
      {
        "displayName": "Isle of Man",
        "countryCode": "IM"
      },
      {
        "displayName": "Israel",
        "countryCode": "IL"
      },
      {
        "displayName": "Italy",
        "countryCode": "IT"
      },
      {
        "displayName": "Jamaica",
        "countryCode": "JM"
      },
      {
        "displayName": "Japan",
        "countryCode": "JP"
      },
      {
        "displayName": "Jersey",
        "countryCode": "JE"
      },
      {
        "displayName": "Jordan",
        "countryCode": "JO"
      },
      {
        "displayName": "Kazakhstan",
        "countryCode": "KZ"
      },
      {
        "displayName": "Kenya",
        "countryCode": "KE"
      },
      {
        "displayName": "Kiribati",
        "countryCode": "KI"
      },
      {
        "displayName": "Korea, Republic of",
        "countryCode": "KR"
      },
      {
        "displayName": "Kosovo",
        "countryCode": "XK"
      },
      {
        "displayName": "Kuwait",
        "countryCode": "KW"
      },
      {
        "displayName": "Kyrgyzstan",
        "countryCode": "KG"
      },
      {
        "displayName": "Laos",
        "countryCode": "LA"
      },
      {
        "displayName": "Latvia",
        "countryCode": "LV"
      },
      {
        "displayName": "Lebanon",
        "countryCode": "LB"
      },
      {
        "displayName": "Lesotho",
        "countryCode": "LS"
      },
      {
        "displayName": "Liberia",
        "countryCode": "LR"
      },
      {
        "displayName": "Libya",
        "countryCode": "LY"
      },
      {
        "displayName": "Liechtenstein",
        "countryCode": "LI"
      },
      {
        "displayName": "Lithuania",
        "countryCode": "LT"
      },
      {
        "displayName": "Luxembourg",
        "countryCode": "LU"
      },
      {
        "displayName": "Macao SAR",
        "countryCode": "MO"
      },
      {
        "displayName": "Madagascar",
        "countryCode": "MG"
      },
      {
        "displayName": "Malawi",
        "countryCode": "MW"
      },
      {
        "displayName": "Malaysia",
        "countryCode": "MY"
      },
      {
        "displayName": "Maldives",
        "countryCode": "MV"
      },
      {
        "displayName": "Mali",
        "countryCode": "ML"
      },
      {
        "displayName": "Malta",
        "countryCode": "MT"
      },
      {
        "displayName": "Marshall Islands",
        "countryCode": "MH"
      },
      {
        "displayName": "Martinique",
        "countryCode": "MQ"
      },
      {
        "displayName": "Mauritania",
        "countryCode": "MR"
      },
      {
        "displayName": "Mauritius",
        "countryCode": "MU"
      },
      {
        "displayName": "Mayotte",
        "countryCode": "YT"
      },
      {
        "displayName": "Mexico",
        "countryCode": "MX"
      },
      {
        "displayName": "Micronesia",
        "countryCode": "FM"
      },
      {
        "displayName": "Moldova",
        "countryCode": "MD"
      },
      {
        "displayName": "Monaco",
        "countryCode": "MC"
      },
      {
        "displayName": "Mongolia",
        "countryCode": "MN"
      },
      {
        "displayName": "Montenegro",
        "countryCode": "ME"
      },
      {
        "displayName": "Montserrat",
        "countryCode": "MS"
      },
      {
        "displayName": "Morocco",
        "countryCode": "MA"
      },
      {
        "displayName": "Mozambique",
        "countryCode": "MZ"
      },
      {
        "displayName": "Myanmar",
        "countryCode": "MM"
      },
      {
        "displayName": "Namibia",
        "countryCode": "NA"
      },
      {
        "displayName": "Nauru",
        "countryCode": "NR"
      },
      {
        "displayName": "Nepal",
        "countryCode": "NP"
      },
      {
        "displayName": "Netherlands",
        "countryCode": "NL"
      },
      {
        "displayName": "New Caledonia",
        "countryCode": "NC"
      },
      {
        "displayName": "New Zealand",
        "countryCode": "NZ"
      },
      {
        "displayName": "Nicaragua",
        "countryCode": "NI"
      },
      {
        "displayName": "Niger",
        "countryCode": "NE"
      },
      {
        "displayName": "Nigeria",
        "countryCode": "NG"
      },
      {
        "displayName": "Niue",
        "countryCode": "NU"
      },
      {
        "displayName": "Norfolk Island",
        "countryCode": "NF"
      },
      {
        "displayName": "North Macedonia",
        "countryCode": "MK"
      },
      {
        "displayName": "Northern Mariana Islands",
        "countryCode": "MP"
      },
      {
        "displayName": "Norway",
        "countryCode": "NO"
      },
      {
        "displayName": "Oman",
        "countryCode": "OM"
      },
      {
        "displayName": "Pakistan",
        "countryCode": "PK"
      },
      {
        "displayName": "Palau",
        "countryCode": "PW"
      },
      {
        "displayName": "Palestinian Authority",
        "countryCode": "PS"
      },
      {
        "displayName": "Panama",
        "countryCode": "PA"
      },
      {
        "displayName": "Papua New Guinea",
        "countryCode": "PG"
      },
      {
        "displayName": "Paraguay",
        "countryCode": "PY"
      },
      {
        "displayName": "Peru",
        "countryCode": "PE"
      },
      {
        "displayName": "Philippines",
        "countryCode": "PH"
      },
      {
        "displayName": "Pitcairn Islands",
        "countryCode": "PN"
      },
      {
        "displayName": "Poland",
        "countryCode": "PL"
      },
      {
        "displayName": "Portugal",
        "countryCode": "PT"
      },
      {
        "displayName": "Puerto Rico",
        "countryCode": "PR"
      },
      {
        "displayName": "Qatar",
        "countryCode": "QA"
      },
      {
        "displayName": "Republic of Congo",
        "countryCode": "CG"
      },
      {
        "displayName": "Reunion",
        "countryCode": "RE"
      },
      {
        "displayName": "Romania",
        "countryCode": "RO"
      },
      {
        "displayName": "Russia",
        "countryCode": "RU"
      },
      {
        "displayName": "Rwanda",
        "countryCode": "RW"
      },
      {
        "displayName": "Saint Barthélemy",
        "countryCode": "BL"
      },
      {
        "displayName": "Saint Helena",
        "countryCode": "SH"
      },
      {
        "displayName": "Saint Kitts and Nevis",
        "countryCode": "KN"
      },
      {
        "displayName": "Saint Lucia",
        "countryCode": "LC"
      },
      {
        "displayName": "Saint Martin",
        "countryCode": "MF"
      },
      {
        "displayName": "Saint Pierre and Miquelon",
        "countryCode": "PM"
      },
      {
        "displayName": "Saint Vincent and the Grenadines",
        "countryCode": "VC"
      },
      {
        "displayName": "Samoa",
        "countryCode": "WS"
      },
      {
        "displayName": "San Marino",
        "countryCode": "SM"
      },
      {
        "displayName": "São Tomé and Príncipe",
        "countryCode": "ST"
      },
      {
        "displayName": "Saudi Arabia",
        "countryCode": "SA"
      },
      {
        "displayName": "Senegal",
        "countryCode": "SN"
      },
      {
        "displayName": "Serbia",
        "countryCode": "RS"
      },
      {
        "displayName": "Seychelles",
        "countryCode": "SC"
      },
      {
        "displayName": "Sierra Leone",
        "countryCode": "SL"
      },
      {
        "displayName": "Singapore",
        "countryCode": "SG"
      },
      {
        "displayName": "Sint Maarten",
        "countryCode": "SX"
      },
      {
        "displayName": "Slovakia",
        "countryCode": "SK"
      },
      {
        "displayName": "Slovenia",
        "countryCode": "SI"
      },
      {
        "displayName": "Solomon Islands",
        "countryCode": "SB"
      },
      {
        "displayName": "Somalia",
        "countryCode": "SO"
      },
      {
        "displayName": "South Africa",
        "countryCode": "ZA"
      },
      {
        "displayName": "South Sudan",
        "countryCode": "SS"
      },
      {
        "displayName": "Spain",
        "countryCode": "ES"
      },
      {
        "displayName": "Sri Lanka",
        "countryCode": "LK"
      },
      {
        "displayName": "Suriname",
        "countryCode": "SR"
      },
      {
        "displayName": "Svalbard and Jan Mayen Island",
        "countryCode": "SJ"
      },
      {
        "displayName": "Swaziland",
        "countryCode": "SZ"
      },
      {
        "displayName": "Sweden",
        "countryCode": "SE"
      },
      {
        "displayName": "Switzerland",
        "countryCode": "CH"
      },
      {
        "displayName": "Taiwan",
        "countryCode": "TW"
      },
      {
        "displayName": "Tajikistan",
        "countryCode": "TJ"
      },
      {
        "displayName": "Tanzania",
        "countryCode": "TZ"
      },
      {
        "displayName": "Thailand",
        "countryCode": "TH"
      },
      {
        "displayName": "Timor-Leste",
        "countryCode": "TL"
      },
      {
        "displayName": "Togo",
        "countryCode": "TG"
      },
      {
        "displayName": "Tokelau",
        "countryCode": "TK"
      },
      {
        "displayName": "Tonga",
        "countryCode": "TO"
      },
      {
        "displayName": "Trinidad and Tobago",
        "countryCode": "TT"
      },
      {
        "displayName": "Tunisia",
        "countryCode": "TN"
      },
      {
        "displayName": "Turkey",
        "countryCode": "TR"
      },
      {
        "displayName": "Turkmenistan",
        "countryCode": "TM"
      },
      {
        "displayName": "Turks and Caicos Islands",
        "countryCode": "TC"
      },
      {
        "displayName": "Tuvalu",
        "countryCode": "TV"
      },
      {
        "displayName": "Uganda",
        "countryCode": "UG"
      },
      {
        "displayName": "Ukraine",
        "countryCode": "UA"
      },
      {
        "displayName": "United Arab Emirates",
        "countryCode": "AE"
      },
      {
        "displayName": "United Kingdom",
        "countryCode": "GB"
      },
      {
        "displayName": "United States",
        "countryCode": "US"
      },
      {
        "displayName": "Uruguay",
        "countryCode": "UY"
      },
      {
        "displayName": "US Minor Outlying Islands",
        "countryCode": "UM"
      },
      {
        "displayName": "Uzbekistan",
        "countryCode": "UZ"
      },
      {
        "displayName": "Vanuatu",
        "countryCode": "VU"
      },
      {
        "displayName": "Venezuela",
        "countryCode": "VE"
      },
      {
        "displayName": "Vietnam",
        "countryCode": "VN"
      },
      {
        "displayName": "Virgin Islands, British",
        "countryCode": "VG"
      },
      {
        "displayName": "Virgin Islands, U.S.",
        "countryCode": "VI"
      },
      {
        "displayName": "Wallis and Futuna",
        "countryCode": "WF"
      },
      {
        "displayName": "Yemen",
        "countryCode": "YE"
      },
      {
        "displayName": "Zambia",
        "countryCode": "ZM"
      },
      {
        "displayName": "Zimbabwe",
        "countryCode": "ZW"
      }
    ]
    }
  },
  mounted(){
    this.$set(document, "title", "空全局创建系统");
  },
  methods: {
    startUp(){
			if(this.username!=''&&this.password!=''){
				this.prestage = true;
        
        axios({
          url: this.baseUrl + '/startUp?time='+Date.now(),
          method: 'POST',
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
          },
          data: { username: this.username, password: this.password }
        }).then(res =>{
				  if (res.data==null||res.data==""){
				    this.msg = '无法获得token，请检查您的凭据';
            this.prestage = false;
				  }
				  else{
            this.token = res.data;
            this.msg = '已获得token，正在请求验证码';
            axios({
              url: this.baseUrl + '/getChallengeCd?time='+Date.now(),
              method: 'POST',
              headers: {
                'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
              },
              data: { token: this.token }
            }).then(res=>{
              this.challengeId = res.data.challengeId;
              this.challengeString = res.data.challengeString;
              this.azureRegion = res.data.azureRegion;
              this.isShowTip = false;
            }).catch(err=>{
              this.msg = '无法获得验证码';
              this.prestage = false;
            });
          }
        }).catch(err=>{
          this.msg = '后台系统出现错误，请重试';
					this.prestage = false;
        })
			}
			else{
				this.msg = '请输入账户和密码';
			}
		},
    reset(){
      this.username = '';
      this.password = '';
      this.inputSolution = '';
      this.challengeString = '';
      this.token = '';
      this.challengeId ='';
      this.azureRegion = '';
      this.location = 'SG';
      this.newTenantId = '';
      this.dirName = '';
      this.prestage = false;
      this.isDeleteUser = false;
      this.isShowTip = true;
      this.disableVerifyBtn = false;
      this.msg = '1. 输入正确的账户和密码后点击开始<br>'+
      '2. 输入正确验证码之后点击验证<br>'+
      '3. 耐心等待一段时间即可获得空全局<br><br>'+
      'PS:如果需要在新全局中删除创建它的帐号，可以勾上删除老账户，系统会自动进行异步删除'
    },
    createTenant(){
			this.disableVerifyBtn = true;
      this.msg = '正在创建空全局,这会花费较长的时间,请耐心等待...';
      this.isShowTip = true;

      axios({
        url: this.baseUrl + '/createTenant?time='+Date.now(),
        method: 'POST',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
        },
        data: {token: this.token, challengeId: this.challengeId, inputSolution: this.inputSolution, location: this.location, azureRegion: this.azureRegion }
      }).then(res=>{
        this.newTenantId = res.data.newTenantId;
        this.dirName = res.data.dirName;

        if(this.newTenantId!=null&&this.dirName!=null&&this.newTenantId!='null'&&this.dirName!='null'){
          this.msg = '已成功创建空全局 '+this.dirName+'('+this.newTenantId+') ,正在新建用户中...';

          axios({
            url:this.baseUrl + '/createUser?time='+Date.now(),
            method:'POST',
            headers: {
              'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
            },
            data : {username: this.username, password: this.password, tenantId: this.newTenantId, dirName: this.dirName, location: this.location, deleteUser: this.isDeleteUser },
          }).then(res=>{
            if(this.isDeleteUser){
              this.msg = '已成功创建新用户 admin@'+this.dirName+'.onmicrosoft.com <br>密码是 '+this.password+'<br>将在新全局中异步删除 '+this.username+' ,您可以继续创建新的空全局';
            }
            else{
              this.msg = '已成功创建新用户 admin@'+this.dirName+'.onmicrosoft.com <br>密码是 '+this.password;
            }
            this.prestage = false;
            this.disableVerifyBtn = false;
          }).catch(err=>{
            this.msg = '创建用户时出现错误:'+err;
            this.prestage = false;
            this.disableVerifyBtn = false;
          })
        }
        else{
          this.msg = '创建失败，可能是验证码错误';
          this.prestage = false;
          this.disableVerifyBtn = false;
        }
      }).catch(err=>{
        this.msg = '后台系统出现错误，请重试';
        this.prestage = false;
        this.disableVerifyBtn = false;
      })
		},
    getChallengeCd(){
      axios({
        url: this.baseUrl + '/getChallengeCd?time='+Date.now(),
        method: 'POST',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
        },
        data: { token: this.token }
      }).then(res=>{
        this.challengeId = res.data.challengeId;
        this.challengeString = res.data.challengeString;
        this.isShowTip = false;
      }).catch(err=>{
        this.msg = '无法获得验证码';
        this.prestage = false;
      });
    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
a {
  color: #42b983;
}

.preinfo{
  position: absolute;
  top: 65px;
  left: 20px;
  right: 20px;
  text-align: left;
}

.startUp{
  width: 100%;
  position: absolute;
  top: 187px;
  text-align: center;
}

.msg1{
  position: absolute;
  top: 217px;
  left: 20px;
  right: 20px;
  font-size: 14px;
  text-align: left;
}

.msg2{
  position: absolute;
  top: 187px;
  left: 20px;
  right: 20px;
  font-size: 14px;
  text-align: center;
}

</style>
