// init chart
var targetArray = 100;
var sectionLen = 5; 
var monthArr = ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
var count = 2;
var dailyArrayTarget1; // json
var contentArr; // date
var weekArr; // week
var arrMonth; // month
var quaterArr; // quater
var chkState = true;
var graphPos = 2; // json
var contentPos = 2; // date
var weekPos = 2; // week
var monthPos = 0;
var chkInc = true;
var currentMonth = 0;
var cnt = 0;
var cntWeek = 0;
var getMonWek = true;
var dataType;
var getCur;
var getFreq = [];
var objId;
var krId;
var leadFreq;
var btnclkHide = true;

jq(document).ready(function(){

	jq(".kr_graph").click(function(){

		reInit();
		
		getFreq = jq(this).attr("data-feqtype"); //  get current graph state
		
		getCur = jq(this).attr("data-target").replace("#","");
		
		var getNew = jq("#"+getCur).find(".hidleadFreqType").val();
		
		getFreq = getNew.split(",");
		
		var getFr = [];
		
		for(var n=0;n<getFreq.length-1;n++){
			if(getFreq[n] == 6) {
				dataType = "date"; // date
				jq("#"+getCur).find(".modal-body").eq(n).find("#dropDown").html("");
				jq("#"+getCur).find(".modal-body").eq(n).find("#dropDown").html('<select name="department" class="form-control"><option value="6">Daily</option><option value="7">Weekly</option><option value="12" >Monthly</option><option value="8" >Quarterly</option></select>');
			}
			else if(getFreq[n] == 7){
				dataType = "week"; // week
				jq("#"+getCur).find(".modal-body").eq(n).find("#dropDown").html("");
				jq("#"+getCur).find(".modal-body").eq(n).find("#dropDown").html('<select name="department" class="form-control"><option value="7" >Weekly</option><option value="12" >Monthly</option><option value="8" >Quarterly</option></select>');
			}
			else if(getFreq[n] == 12) {
				dataType = "month"; // month
				jq("#"+getCur).find(".modal-body").eq(n).find("#dropDown").html("");
				jq("#"+getCur).find(".modal-body").eq(n).find("#dropDown").html('<select name="department" class="form-control"><option value="12" >Monthly</option><option value="8" >Quarterly</option></select>');
			}
			else if(getFreq[n] == 8) {
				dataType = "quater"; // quater
				jq("#"+getCur).find(".modal-body").eq(n).find("#dropDown").html("");
				jq("#"+getCur).find(".modal-body").eq(n).find("#dropDown").html('<select name="department" class="form-control"><option value="8" >Quarterly</option></select>');
			}
		}
		
		getDatatype(getFreq);
		
		objId = jq(this).attr("data-obid");
		krId = jq(this).attr("data-krid");
		leadFreq = jq(this).attr("data-feqtype");
		
		jq("#"+getCur).find("select").change(function () {
			var str = jq(this).val();
			var arrlead = "";
			for(var c1=0;c1<jq("#"+getCur).find(".custom-dropdown").find("select").length;c1++){
				arrlead += jq("#"+getCur).find(".custom-dropdown").find("select").eq(c1).find("option:selected").val()+",";
			}
			var leadArr = arrlead.toString();
			var pos = leadArr.lastIndexOf(',');
			leadArr = leadArr.substring(0,pos)+''+leadArr.substring(pos+1);
			invokePhp(jq(".kr_graph"),leadArr,getCur);
		});
		
		getFreq = "";
		invokePhp(this,getFreq,getCur);
		
	});
	
});

function reInit(){
		 targetArray = 100;
		 sectionLen = 5; 
		 monthArr = ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
		 count = 2;
		 dailyArrayTarget1; // json
		 contentArr; // date
		 weekArr; // week
		 arrMonth; // month
		 quaterArr; // quater
		 chkState = true;
		 graphPos = 2; // json
		 contentPos = 2; // date
		 weekPos = 2; // week
		 monthPos = 0;
		 chkInc = true;
		 currentMonth = 0;
		 cnt = 0;
		 cntWeek = 0;
		 getMonWek = true;
		 getFreq = [];
		 btnclkHide = true;
}


function getDatatype(getFreq){
		if(getFreq == 6) {
			dataType = "date"; // date
		}
		else if(getFreq == 7){
			dataType = "week"; // week
		}
		else if(getFreq == 12) {
			dataType = "month"; // month
		}
		else if(getFreq == 8) {
			dataType = "quater"; // quater
		}
}



function  invokePhp(cur,getL,curPop){
	reInit();
		jq.post("https://xone.xerago.com/xone/wp-content/plugins/pms/getLeadJson.php",{
			userId:jq("#hidUserId").val(),		
			objId:objId,
			krId:krId,
			leadFreq:leadFreq,
			leadView:getL,
			viewYear:jq("#hidViewYear").val(),
			viewQuarter:jq("#hidViewQuarter").val()
		}, function( data ) {
			jq(".jsonVal").html(data);
			jq("#"+curPop).find(".mainContainer").html("");
			setTimeout(function(){
				jq("#"+curPop).find(".mainContainer").find(".mainDiv").remove();
				createChart();
				btnClick();
			},500);
		});
}



function createChart(){
	currentMonth = (new Date).getMonth() + 1;
	getIndex = currentMonth;
	
	for(var i=1;i<=count;i++){
		var getStr = json["LM"+i];
		chkInc = true;
		
		if(getStr.indexOf('+') != -1) dailyArrayTarget1 = createArray(getStr);
		else dailyArrayTarget1 = arrayFn(getStr);
		
		
		var n = getStr.split("+");
		var len = n.length;
			
		if(len <= 2){
			navHideShow = false;
		}
		else{
			navHideShow = true;
		}
		
		
		if(arrLeadView[i-1] == 6) {
			dataType = "date";
			sectionLen = 5;
		}
		if(arrLeadView[i-1] == 7) {
			dataType = "week";
			sectionLen = 5;
		}
		if(arrLeadView[i-1] == 12){
			dataType = "month";
			sectionLen = 3;
		}
		if(arrLeadView[i-1] == 8){
			dataType = "quater";
			sectionLen = 1;
		}
		
		
		if(i == 1) graphPos = json.c1; 
		if(i == 2) graphPos = json.c2; 
		if(i == 3) graphPos = json.c3; 
		if(i == 4) graphPos = json.c4; 
		if(i == 5) graphPos = json.c5; 
		if(i == 6) graphPos = json.c6; 
		if(i == 7) graphPos = json.c7; 
		if(i == 8) graphPos = json.c8; 
		if(i == 9) graphPos = json.c9; 
		if(i == 10) graphPos = json.c10; 
		if(i == 11) graphPos = json.c11; 
		if(i == 12) graphPos = json.c12; 
		
		
		if(dataType == "date"){
			var getStr1 = leadViewFreq["LM"+i];
			if(getStr1.indexOf('+') != -1) {		
				chkInc = false;
				contentArr = createArray(getStr1);	
			
			}
			else{			
				contentArr = arrayFn(getStr1);
				
			}
			if(i == 1) contentPos = leadViewFreq.c1; 
			if(i == 2) contentPos = leadViewFreq.c2; 
			if(i == 3) contentPos = leadViewFreq.c3; 
			if(i == 4) contentPos = leadViewFreq.c4; 
			if(i == 5) contentPos = leadViewFreq.c5; 
			if(i == 6) contentPos = leadViewFreq.c6; 
			if(i == 7) contentPos = leadViewFreq.c7; 
			if(i == 8) contentPos = leadViewFreq.c8; 
			if(i == 9) contentPos = leadViewFreq.c9; 
			if(i == 10) contentPos = leadViewFreq.c10; 
			if(i == 11) contentPos = leadViewFreq.c11; 
			if(i == 12) contentPos = leadViewFreq.c12; 
			
		}
		if(dataType == "week"){
			sectionLen = 5;
			var getStr1 = leadViewFreq["LM"+i];
			leadViewFreq["cur"+i] = currentMonth;
			if(getStr1.indexOf('+') != -1) {	
				chkInc = false;			
				weekArr = createArray(getStr1);

			}
			else{			
				weekArr = arrayFn(getStr1);
				
			}
			if(i == 1) weekPos = leadViewFreq.c1; 
			if(i == 2) weekPos = leadViewFreq.c2; 
			if(i == 3) weekPos = leadViewFreq.c3; 
			if(i == 4) weekPos = leadViewFreq.c4; 
			if(i == 5) weekPos = leadViewFreq.c5; 
			if(i == 6) weekPos = leadViewFreq.c6; 
			if(i == 7) weekPos = leadViewFreq.c7; 
			if(i == 8) weekPos = leadViewFreq.c8; 
			if(i == 9) weekPos = leadViewFreq.c9; 
			if(i == 10) weekPos = leadViewFreq.c10; 
			if(i == 11) weekPos = leadViewFreq.c11; 
			if(i == 12) weekPos = leadViewFreq.c12; 
		
			
		}
		if(dataType == "month"){
			
			jq(".verticalDiv").html("");
			graphPos = 0;	

			sectionLen = 3;
			var getStr1 = leadViewFreq["LM"+i];
			if(getStr1.indexOf('+') != -1) {		
				chkInc = false;
				arrMonth = createArray(getStr1);
			}
			else{			
				arrMonth = arrayFn(getStr1);
			}
			if(i == 1) monthPos = leadViewFreq.c1; 
			if(i == 2) monthPos = leadViewFreq.c2; 
			if(i == 3) monthPos = leadViewFreq.c3; 
			if(i == 4) monthPos = leadViewFreq.c4; 
			if(i == 5) monthPos = leadViewFreq.c5; 
			if(i == 6) monthPos = leadViewFreq.c6; 
			if(i == 7) monthPos = leadViewFreq.c7; 
			if(i == 8) monthPos = leadViewFreq.c8; 
			if(i == 9) monthPos = leadViewFreq.c9; 
			if(i == 10) monthPos = leadViewFreq.c10; 
			if(i == 11) monthPos = leadViewFreq.c11; 
			if(i == 12) monthPos = leadViewFreq.c12; 
		}
		if(dataType == "quater"){
			jq(".verticalDiv").html("");
			graphPos = 0;	
		
			sectionLen = 1;
			
			var getStr1 = leadViewFreq["LM"+i];
			if(getStr1.indexOf('+') != -1) {		
				chkInc = false;
				quaterArr = createArray(getStr1);
			}
			else{			
				quaterArr = arrayFn(getStr1);
			}
			if(i == 1) quaterPos = leadViewFreq.c1; 
			if(i == 2) quaterPos = leadViewFreq.c2; 
			if(i == 3) quaterPos = leadViewFreq.c3; 
			if(i == 4) quaterPos = leadViewFreq.c4; 
			if(i == 5) quaterPos = leadViewFreq.c5; 
			if(i == 6) quaterPos = leadViewFreq.c6; 
			if(i == 7) quaterPos = leadViewFreq.c7; 
			if(i == 8) quaterPos = leadViewFreq.c8; 
			if(i == 9) quaterPos = leadViewFreq.c9; 
			if(i == 10) quaterPos = leadViewFreq.c10; 
			if(i == 11) quaterPos = leadViewFreq.c11; 
			if(i == 12) quaterPos = leadViewFreq.c12; 

		}
	
		
		chart(i);
		popUpFn();
		
		jq(".rightNav").hide();
	}
	
	
	//updateFunc();
}



function  updateFunc(){
	var updateCont;
	
	jq(".mainContainer").find(".fileDiv").remove();
	
	for(var i=0;i<count;i++){
		updateCont = jq('<div id="fileDiv'+i+'" class="fileDiv"><div class="dropdown jd-dropdown"><button class="btn btn-default dropdown-toggle" type="button" id="dropdownMenu1" data-toggle="dropdown" aria-expanded="true">Upload Exel</button><div class="dropdown-menu" role="menu" aria-labelledby="dropdownMenu1"><div class="row"><div class="col-xs-12 col-sm-12 col-md-12 col-lg-12 "><form class="" action="jd_import.php" method="post" name="upload_excel" enctype="multipart/form-data"><div class="form-group"><label>Import CSV File:</label><input type="file" name="file" id="file" class="small"></div><div class="form-group"><button type="submit" id="submit" name="Import" class="btn btn-primary button-loading btn-xs" data-loading-text="Loading...">Upload</button><button type="submit" id="submit" name="clear" class="btn btn-primary button-loading btn-xs" data-loading-text="Loading...">Delete</button></div></form></div></div></div></div><div class="dropdown jd-dropdown"><button class="btn btn-default dropdown-toggle" type="button" id="updateMenu1" data-toggle="dropdown" aria-expanded="true">Update</button><div class="dropdown-menu update-menu" role="menu" aria-labelledby="updateMenu1"><div class="row"><div class="col-xs-12 col-sm-12 col-md-12 col-lg-12 "><form class="" action="import_form.php" method="post" name="upload_form" enctype="multipart/form-data"><div class="form-group"><input type="text" placeholder="Target" name="target1"/><input type="text" placeholder="Achieved" name="achieved1"/><input type="text" value="Week1" name="leadViewFreq1" readonly /></div><div class="form-group"><input type="text" placeholder="Target" name="target2"/><input type="text" placeholder="Achieved" name="achieved2"/><input type="text" value="Week2" name="leadViewFreq2" readonly /></div><div class="form-group"><input type="text" placeholder="Target" name="target3"/><input type="text" placeholder="Achieved" name="achieved3"/><input type="text" value="Week3" name="leadViewFreq3" readonly /></div><div class="form-group"><input type="text" placeholder="Target" name="target4"/><input type="text" placeholder="Achieved" name="achieved4"/><input type="text" value="Week4" name="leadViewFreq4" readonly /></div><div class="form-group"><button type="submit" id="submit" name="import" class="btn btn-primary button-loading btn-xs" data-loading-text="Loading...">Submit</button><button type="reset" id="submit" name="clear" class="btn btn-primary button-loading btn-xs" data-loading-text="Loading...">Reset</button></div></form></div></div></div></div></div>');
		
		jq("#mainDiv"+(i+1)).append(updateCont);
	}
}



function btnClick(){

	jq(".leftNav").click(function(){
		
		chkState = false;
		navHideShow = false;
		btnclkHide = false;
		
		var getId = jq(this).parent().attr("id");
		var getNo = getId.substr(7,1);
		var curGp = "c"+getNo;
		
		
		if(arrLeadView[getNo-1] == 6){
			dataType = "date";
			sectionLen = 5;
		}
		if(arrLeadView[getNo-1] == 7){
			dataType = "week";
			sectionLen = 5;
		}
		if(arrLeadView[getNo-1] == 12){
			dataType = "month";
			sectionLen = 3;
		}
		if(arrLeadView[getNo-1] == 8){
			dataType = "quater";
			sectionLen = 1;
		}
		
		if(getNo == 1) graphPos = json.c1; 
		if(getNo == 2) graphPos = json.c2;
		if(getNo == 3) graphPos = json.c3; 
		if(getNo == 4) graphPos = json.c4; 
		if(getNo == 5) graphPos = json.c5; 
		if(getNo == 6) graphPos = json.c6; 
		if(getNo == 7) graphPos = json.c7; 
		if(getNo == 8) graphPos = json.c8; 
		if(getNo == 9) graphPos = json.c9; 
		if(getNo == 10) graphPos = json.c10; 
		if(getNo == 11) graphPos = json.c11; 
		if(getNo == 12) graphPos = json.c12;
		
		if(dataType == "date"){
			if(getNo == 1) contentPos = leadViewFreq.c1; 
			if(getNo == 2) contentPos = leadViewFreq.c2;
			if(getNo == 3) contentPos = leadViewFreq.c3; 
			if(getNo == 4) contentPos = leadViewFreq.c4; 
			if(getNo == 5) contentPos = leadViewFreq.c5; 
			if(getNo == 6) contentPos = leadViewFreq.c6; 
			if(getNo == 7) contentPos = leadViewFreq.c7; 
			if(getNo == 8) contentPos = leadViewFreq.c8; 
			if(getNo == 9) contentPos = leadViewFreq.c9; 
			if(getNo == 10) contentPos = leadViewFreq.c10; 
			if(getNo == 11) contentPos = leadViewFreq.c11; 
			if(getNo == 12) contentPos = leadViewFreq.c12; 
		}
		else if(dataType == "week"){
			if(getNo == 1) weekPos = leadViewFreq.c1; 
			if(getNo == 2) weekPos = leadViewFreq.c2;
			if(getNo == 3) weekPos = leadViewFreq.c3; 
			if(getNo == 4) weekPos = leadViewFreq.c4; 
			if(getNo == 5) weekPos = leadViewFreq.c5; 
			if(getNo == 6) weekPos = leadViewFreq.c6; 
			if(getNo == 7) weekPos = leadViewFreq.c7; 
			if(getNo == 8) weekPos = leadViewFreq.c8; 
			if(getNo == 9) weekPos = leadViewFreq.c9; 
			if(getNo == 10) weekPos = leadViewFreq.c10; 
			if(getNo == 11) weekPos = leadViewFreq.c11; 
			if(getNo == 12) weekPos = leadViewFreq.c12; 
		}
		
		if(graphPos == 1) {
			var getcurID = jq(this).parent().attr("id");
			jq("#"+getCur).find(".modal-body").eq(getNo-1).find(".mainContainer").find(".mainDiv").find(".leftNav").hide();
			jq("#"+getCur).find(".modal-body").eq(getNo-1).find(".mainContainer").find(".mainDiv").find(".rightNav").show();
		}
		else{
			var getcurID = jq(this).parent().attr("id");
			jq("#"+getCur).find(".modal-body").eq(getNo-1).find(".mainContainer").find(".mainDiv").find(".leftNav").show();
			jq("#"+getCur).find(".modal-body").eq(getNo-1).find(".mainContainer").find(".mainDiv").find(".rightNav").show();
		}
		
		graphPos = graphPos-1;
		
		if(dataType == "date") contentPos = contentPos-1;
		else if(dataType == "week") weekPos = weekPos-1;
		
		
		var getId = jq(this).parents().attr("id");
		var val = getId.substr(7, 1);
		var getStr = json["LM"+val];
		chkInc = false;
		

		dailyArrayTarget1 = createArray(getStr,getNo);	
		
		
		if(dataType == "date"){
			var getStr1 = leadViewFreq["LM"+val];
			contentArr = createArray(getStr1,getNo);	
			getCurrentMonth(contentArr,this);
		}
		
		else if(dataType == "week"){
			var getStr1 = leadViewFreq["LM"+val];
			weekArr = createArray(getStr1,getNo);	
			getMonWek = false;
			var getD = ("cur"+val).toString();
			leadViewFreq[getD] = leadViewFreq[getD]-1;
			getMonthforWeek(weekArr,this,leadViewFreq[getD]);
			
		}
		
		
		chart(val);
		popUpFn();
		
		json[curGp] = parseInt(graphPos);
		
		if(dataType == "date") leadViewFreq[curGp] = parseInt(contentPos);
		else if(dataType == "week") leadViewFreq[curGp] = parseInt(weekPos);
		
		
	});
	jq(".rightNav").click(function(){
		chkState = false;
		navHideShow = false;
		btnclkHide = false;
		
		var getId = jq(this).parent().attr("id");
		var getNo = getId.substr(7,1);
		var curGp = "c"+getNo;
		
		if(arrLeadView[getNo-1] == 6){
			dataType = "date";
			sectionLen = 5;
		}
		if(arrLeadView[getNo-1] == 7){
			dataType = "week";
			sectionLen = 5;
		}
		if(arrLeadView[getNo-1] == 12){
			dataType = "month";
			sectionLen = 3;
		}
		if(arrLeadView[getNo-1] == 8){
			dataType = "quater";
			sectionLen = 1;
		}
		
		
		
		if(getNo == 1) graphPos = json.c1; 
		if(getNo == 2) graphPos = json.c2; 
		if(getNo == 3) graphPos = json.c3; 
		if(getNo == 4) graphPos = json.c4; 
		if(getNo == 5) graphPos = json.c5; 
		if(getNo == 6) graphPos = json.c6; 
		if(getNo == 7) graphPos = json.c7; 
		if(getNo == 8) graphPos = json.c8;  
		if(getNo == 9) graphPos = json.c9; 
		if(getNo == 10) graphPos = json.c10; 
		if(getNo == 11) graphPos = json.c11; 
		if(getNo == 12) graphPos = json.c12; 
		
		
		if(dataType == "date"){
			if(getNo == 1) contentPos = leadViewFreq.c1; 
			if(getNo == 2) contentPos = leadViewFreq.c2; 
			if(getNo == 3) contentPos = leadViewFreq.c3; 
			if(getNo == 4) contentPos = leadViewFreq.c4; 
			if(getNo == 5) contentPos = leadViewFreq.c5; 
			if(getNo == 6) contentPos = leadViewFreq.c6; 
			if(getNo == 7) contentPos = leadViewFreq.c7; 
			if(getNo == 8) contentPos = leadViewFreq.c8; 
			if(getNo == 9) contentPos = leadViewFreq.c9; 
			if(getNo == 10) contentPos = leadViewFreq.c10; 
			if(getNo == 11) contentPos = leadViewFreq.c11; 
			if(getNo == 12) contentPos = leadViewFreq.c12; 
		}
		else if(dataType == "week"){
			if(getNo == 1) weekPos = leadViewFreq.c1; 
			if(getNo == 2) weekPos = leadViewFreq.c2;
			if(getNo == 3) weekPos = leadViewFreq.c3; 
			if(getNo == 4) weekPos = leadViewFreq.c4; 
			if(getNo == 5) weekPos = leadViewFreq.c5; 
			if(getNo == 6) weekPos = leadViewFreq.c6; 
			if(getNo == 7) weekPos = leadViewFreq.c7; 
			if(getNo == 8) weekPos = leadViewFreq.c8; 
			if(getNo == 9) weekPos = leadViewFreq.c9; 
			if(getNo == 10) weekPos = leadViewFreq.c10; 
			if(getNo == 11) weekPos = leadViewFreq.c11; 
			if(getNo == 12) weekPos = leadViewFreq.c12; 
		}
		
		if(graphPos == dailyArrayTarget1.length-2){
			var getcurID = jq(this).parent().attr("id");
			jq("#"+getCur).find(".modal-body").eq(getNo-1).find(".mainContainer").find(".mainDiv").find(".rightNav").hide();
			jq("#"+getCur).find(".modal-body").eq(getNo-1).find(".mainContainer").find(".mainDiv").find(".leftNav").show();
		}
		else{
			var getcurID = jq(this).parent().attr("id");
			jq("#"+getCur).find(".modal-body").eq(getNo-1).find(".mainContainer").find(".mainDiv").find(".leftNav").show();
			jq("#"+getCur).find(".modal-body").eq(getNo-1).find(".mainContainer").find(".mainDiv").find(".rightNav").show();
		}
		
		graphPos = graphPos+1;
		
		if(dataType == "date") contentPos = contentPos+1;
		else if(dataType == "week") weekPos = weekPos+1;
		
		
		var getId = jq(this).parents().attr("id");
		var val = getId.substr(7, 1);
		var getStr = json["LM"+val];
		chkInc = false;
		dailyArrayTarget1 = createArray(getStr);
		
		if(dataType == "date"){
			var getStr1 = leadViewFreq["LM"+val];
			contentArr = createArray(getStr1);	
			
			getCurrentMonth(contentArr,this);
		}
		else if(dataType == "week"){
			getMonWek = true;
			var getStr1 = leadViewFreq["LM"+val];
			weekArr = createArray(getStr1);	
			var getD = ("cur"+val).toString();
			leadViewFreq[getD] = leadViewFreq[getD]+1;
			getMonthforWeek(weekArr,this,leadViewFreq[getD]);
		}
		
		chart(val);
		popUpFn();
		
		json[curGp] = parseInt(graphPos);
		
		if(dataType == "date") leadViewFreq[curGp] = parseInt(contentPos);
		else if(dataType == "week") leadViewFreq[curGp] = parseInt(weekPos);
		
	});
}

var navHideShow = false;
function chart(c){
	
		if(chkState){
			jq("#"+getCur).find(".mainContainer").eq(c-1).append('<div class="mainDiv" id="mainDiv'+c+'"><div class="leftDiv"><div class="horizontalDiv">RANGE</div></div><div class="rightDiv"><div class="rightDivlineparent"></div></div><div class="bottomrightDiv"></div><div class="verticalDiv">'+monthArr[currentMonth-1]+'</div></div>');
		}	
		
		
		if(navHideShow){
			jq("#"+getCur).find(".mainContainer").eq(c-1).find(".mainDiv").append('<div class="leftNav"><span class="arrow_icon"></span></div><div class="rightNav"><span class="arrow_icon"></span></div>');
		}
		
		
			jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".leftDiv").find(".innerLeft").remove();
			jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".rightDiv").find(".rightDivlineparent").html("");
			jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".bottomrightDiv").html("");
			jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".rightDiv").find(".innerRight").remove();
		
		
		var getNum = Math.max.apply(Math,dailyArrayTarget1[graphPos]);
		var maxNoLen = getNum.toString().length;
		var gethorVal = jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".horizontalDiv").css("right");
		
		/* if(getNum< 50) var getMax = Math.ceil(getNum / 5) * 5;
		else  var getMax = Math.ceil(getNum / 50) * 50; */
		
		var getMax = 100;
		var findMaxValue = getMax;
		var leftLen = 5;
		var leftHeight = jq(".leftDiv").height()/leftLen;
		var indH = findMaxValue/5;
		
		for(var i=0;i<leftLen;i++){	
			
			jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".leftDiv").append("<div class=\"innerLeft\"><span style=\"position: absolute; right: 10px; top: -8px;\">"+(findMaxValue-(indH*i))+"</span></div>");
			jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".rightDiv").find(".rightDivlineparent").append("<div class=\"rightDivline\"></div>");
			
		}
		
		jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".innerLeft").css({"width":"100%","border-top":"1px solid rgba(0, 0, 0, 0)","text-align":"center","position":"relative"});
		jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".rightDivline").css({"width":"100%","border-top":"1px solid #ccc","text-align":"center"});
		 
		var rightLen = dailyArrayTarget1[graphPos].length;
		
		var rightWidth = (100/sectionLen);
		var findPX = jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".rightDiv").height()/findMaxValue;
		
		for(var j=0;j<rightLen-1;j++){
			if(dailyArrayTarget1[graphPos][j] < 50 && dailyArrayTarget1[graphPos][j] > 25){
				jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".rightDiv").append("<div class=\"innerRight\"><div class=\"chartBar orangeClr\"></div><span id=\"popUp\">Target:"+targetArray+"</br>"+"Achieved:"+dailyArrayTarget1[graphPos][j]+"</span></div>");
			}
			else if(dailyArrayTarget1[graphPos][j] < 25){
				jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".rightDiv").append("<div class=\"innerRight\"><div class=\"chartBar redClr\"></div><span id=\"popUp\">Target:"+targetArray+"</br>"+"Achieved:"+dailyArrayTarget1[graphPos][j]+"</span></div>");
			}
			else{
				jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".rightDiv").append("<div class=\"innerRight\"><div class=\"chartBar\"></div><span id=\"popUp\">Target:"+targetArray+"</br>"+"Achieved:"+dailyArrayTarget1[graphPos][j]+"</span></div>");
			}
			
			
			jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".chartBar").css("width","80%");
					
			jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".chartBar").eq(j).animate({"height":(dailyArrayTarget1[graphPos][j]*findPX)+"px"}, 700);
			
			
			var getWid = jq(".innerRight").eq(j).find("span").width()
			
			if(jq(".innerRight").eq(j).find("span").width() < 0 || jq(".innerRight").eq(j).find("span").width() == null) getWid = 88;
			
			jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".innerRight").eq(j).find("span").css({"padding": "10px", "position":"absolute","left":"50%","bottom":((dailyArrayTarget1[graphPos][j]*findPX)+20)+"px","width":"auto","margin-left":-(getWid/2)-11+"px"});
			
			
			jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".rightDivline").eq(j).css({"height":((leftHeight)-1)+"px"});
		}
		for(var j=0;j<5;j++){
			jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".innerLeft").eq(j).css({"height":"58.8px"});
		}
		
		if(dataType == "date"){
			for(var j1=0;j1<contentArr[contentPos].length;j1++){
					jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".bottomrightDiv").append("<div class=\"innerRight\"><div>"+contentArr[contentPos][j1].replace("-2015","")+"</div></div>");
			}
		}
		else if(dataType == "week"){
			for(var j1=0;j1<weekArr[weekPos].length;j1++){
					jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".bottomrightDiv").append("<div class=\"innerRight\"><div>"+weekArr[weekPos][j1]+"</div></div>");
			}
		}
		else if(dataType == "month"){
			for(var j1=0;j1<arrMonth[0].length;j1++){
					jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".bottomrightDiv").append("<div class=\"innerRight\"><div>"+arrMonth[0][j1]+"</div></div>");
			}
			jq(".verticalDiv").html("");
		}
		else if(dataType == "quater"){
			for(var j1=0;j1<quaterArr[0].length;j1++){
					jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".bottomrightDiv").append("<div class=\"innerRight\"><div>"+quaterArr[0][j1]+"</div></div>");
			}
			jq(".verticalDiv").html("");
		}
		
		jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".innerRight").css("width",rightWidth+"%");	
		jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".innerRight").css({"position":"relative","height":"100%","display":"inline-block","text-align":"center","margin":"0","z-index":"2"});
		jq("#"+getCur).find(".mainContainer").eq(c-1).find("#mainDiv"+c).find(".chartBar").css({"display":"inline-block","text-align":"center","margin":"0","bottom":" 0","position": "absolute","left":"12%"});
	
		popUpFn();
}


function popUpFn(){
	jq(".chartBar").mousemove(function(e){
			jq(e.target).parent().css("z-index","3");
			jq(e.target).parent().find("#popUp").show();
	});
	jq(".chartBar").mouseleave(function(e){
			jq(e.target).parent().find("#popUp").hide();
			jq(".rightDiv").find(".innerRight").css("z-index","2");
	});
}



function createArray(str,noval){
	if(chkInc) cnt++;
	var s = str;
	var getVal = s.split("+");
	
	if(noval) cnt = noval;	
	
	if(count >= cnt && btnclkHide) {
		json["c"+cnt] = parseInt(getVal.length-2);
		leadViewFreq["c"+cnt] = parseInt(getVal.length-2);
	}
	
	var arr = [];
	var n = s.split("+");
	var len = n.length;
	
	if(dataType == "date"){
		for(var i1=0;i1<len;i1++){ 
			var getEmp = n[i1];
			var lengstr = getEmp.split(",");
			var getLen = lengstr.length;
			if(i1 != len-2){				
				for(var ln=getLen;ln<=5;ln++) {
					lengstr.unshift("-");
					n[i1] = lengstr.toString();
				}
			}
			else{
				if(i1 != (len)){
					lengstr.pop();
					for(var ln=getLen;ln<=5;ln++) {
						lengstr.push("-");
						n[i1] = lengstr.toString();
					}
				}
			}
		}	
	}
	else if(dataType == "week"){
		for(var i1=0;i1<len;i1++){ 
			var getEmp = n[i1];
			var lengstr = getEmp.split(",");
			var getLen = lengstr.length;
			if(i1 != len-2){				
				for(var ln=getLen;ln<=5;ln++) {
					lengstr.unshift("-");
					n[i1] = lengstr.toString();
				}
			}
			else{
				if(i1 != (len)){
					lengstr.pop();
					for(var ln=getLen;ln<=5;ln++) {
						lengstr.push("-");
						n[i1] = lengstr.toString();
					}
				}
			}
		}	
	}
	
	
	for(var i=0;i<len-1;i++){ 
		arr[i] = n[i].split(",");
	}
	
	return arr;
}

function arrayFn(getStr){
	var arrNew = [];
	var s = getStr.split(",");
	arrNew.push(s);
	return arrNew;
}


function getCurrentMonth(getArr,cur){
	var getDate = getArr[contentPos][getArr[contentPos].length-2];
	var getMonthData = getDate.split("-");
	var getFinal = parseInt(getMonthData[1])-1;
	
	var getcurID = jq(cur).parent().attr("id");
	//jq("#"+getcurID).find(".verticalDiv").html("");
	jq("#"+getcurID).find(".verticalDiv").html(monthArr[getFinal]);
	
}

var getIndex;

function getMonthforWeek(getArr,cur,val){
 	var getcurID = jq(cur).parent().attr("id");
	jq("#"+getcurID).find(".verticalDiv").html("");
	jq("#"+getcurID).find(".verticalDiv").html(monthArr[val-1]);  
}
