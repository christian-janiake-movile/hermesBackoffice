var application = new Object();
var pageSize = 13;
var endOfList = false;
var appIdSelected = "";
var pincodeSelected = "";
var lastKeyStack = [];
var modal = false;

function next() {
	if(!modal && !endOfList) {
		pincodeSelected = "";
		if(lastKeyStack.length > 0) {
			var lk = lastKeyStack[lastKeyStack.length - 1];
			loadCodes(lk);
		}
	}
}			
			
function prev() {
	if(!modal) {
		pincodeSelected = "";
		endOfList = false;
		lastKeyStack.pop();
		lastKeyStack.pop();
		var lk = lastKeyStack[lastKeyStack.length - 1];
		loadCodes(lk);
	}
}			
			
function loadAppCodes(obj,appId) {
	if(!modal) {
		pincodeSelected = "";
		endOfList = false;
		$(".chosen").addClass("selitem").removeClass("chosen");
		obj.className = "chosen";
		lastKeyStack = [];	
		appIdSelected = appId;
		loadCodes(null);
	}
}
			
function choosePincode(obj,pincode) {
	if(!modal) {
		$(".pinchosen").addClass("selitem").removeClass("pinchosen");
		obj.className = "pinchosen";
		pincodeSelected = pincode;
	}
}
						
function loadCodes(lastkey) {
	var url = "http://hermeshomolog:4768/api/1.0/promocode?application_id=" + appIdSelected + "&limit=" + pageSize;
	if(lastkey) {
		url = url + "&last_key=" + lastkey;
	}
	$.getJSON(url , function( data ) {
		var content = "";
		var lastpin = "";
		var count = 0;
		$.each( data, function( i, item ) {
			var pincode = item.pincode;
			var desc = item.description;
			content = content + '<p class="selitem" onclick="choosePincode(this,\'' + pincode + '\')">' + pincode + ' (' + desc + ')</p>';
			lastpin = pincode;
			count++;
		});
		lastKeyStack.push(lastpin);
		if(content =="") {
			content = "No promocode available";
		}
		if(count < pageSize) {
			endOfList = true;
		}
		$("#rightcol").html(content);
	});
}
			
function modal_generate() {
	if(!modal) {
		if(appIdSelected == "") {
			$("#alert_text").html("Please choose the application!");
			$("#alert_red").show();				
		} else {
			$("input").val("");
			$("#alert_red").hide();
			$("#dialog_generate").show();
			$("#field_validation_alert").hide();
			$("#appId").val(appIdSelected);
			modal = true;
			$("#btgenjson").prop('disabled', false);
			$("#pngenjson").hide();
		}
	}
}

function modal_reserve() {
	if(!modal) {
		if(appIdSelected == "") {
			$("#alert_text").html("Please choose the application!");
			$("#alert_red").show();				
		} else if(pincodeSelected == "") {
			$("#alert_text").html("Please choose the pincode!");
			$("#alert_red").show();				
		} else {
			$("#alert_red").hide();
			$("#dialog_reserve").show();
			$("#pincode").val(pincodeSelected);
			modal = true;
		}
	}
}
			
function close_generate() {
	$("#dialog_generate").hide();
	modal = false;
}

function close_reserve() {
	$("#dialog_reserve").hide();
	modal = false;
}

function generate() {
	var description = $("#description").val();
	if(description == "") {
			$("#field_validation_text").html("Description is mandatory!");
			$("#field_validation_alert").show();
			$("#description").focus();
			return false;				
	}
	var quantity = $("#qty").val();
	if(quantity == "") {
			$("#field_validation_text").html("Quantity is mandatory!");
			$("#field_validation_alert").show();
			$("#qty").focus();
			return false;				
	}
	var appData = $("#appData").val();
	if(appData == "") {
			$("#field_validation_text").html("Application Data is mandatory!");
			$("#field_validation_alert").show();
			$("#appData").focus();
			return false;				
	}
	$("#dialog_generate").hide();
	modal = false;
	var url = "http://hermeshomolog:4768/api/1.0/promocode/generate?application_id=" + appIdSelected +
		"&description=" + encodeURIComponent(description) +
		"&application_data=" + encodeURIComponent(appData) +
		"&batch_size=" + quantity;
	$.getJSON(url , function( data ) {
		lastKeyStack = [];
		endOfList = false;
		loadCodes(null);
	});
				
}

function reserve() {
	$("#dialog_reserve").hide();
	modal = false;
	var url = "http://hermeshomolog:4768/api/1.0/promocode/" + pincodeSelected + "/reserve";
	$.getJSON(url , function( data ) {
		lastKeyStack = [];
		endOfList = false;
		loadCodes(null);
	});
}
			
$(document).ready(function() {
	$("#dialog_generate").hide();
	$("#dialog_reserve").hide();
	$("#alert_red").hide();
	$("#pngenjson").hide();
	$("button.close").on('click', function () {
		$("div.alert-dismissable").hide();
	});
	$("#btgenjson").on('click', function() {
		$("#btgenjson").prop('disabled', true);
		$("#pngenjson").show();
	});
	$("button.removeAttr").on('click', function() {
		$(this).parent().parent().remove();
	});
	$("#addAttr").on('click', function() {
		$("#pnbodygenjson").append("<div class='input-group'>" +
		"<span class='input-group-addon'>Key</span>" +
		"<input type='text' class='form-control jsonkey'>" +
		"<span class='input-group-addon'>Value</span>" +
		"<input type='text' class='form-control jsonvalue'>" +
		"<span class='input-group-btn'>" +
		"<button class='btn btn-danger removeAttr' type='button'>Remove</button>" +
		"</span>" +
		"</div>");
		$("button.removeAttr").on('click', function() {
			$(this).parent().parent().remove();
		});
	});
	$("#genjson").on('click', function() {
		var keys=[];
		$("input.jsonkey").each(function( index ) {
			keys.push($(this).val());
		});
		var values=[];
		$("input.jsonvalue").each(function( index ) {
			values.push($(this).val());
		});
		
		var result = "";
		$.each(keys, function( index, value ) {
			if(result == "") {
				result = "{";
			} else {
				result = result + ",";
			}
			result = result + '"' + value + '":"' + values[index] + '"';
		});
		if(result != "") {
			result = result + "}";
		}
		$("#appData").val(result);
		$("#btgenjson").prop('disabled', false);
		$("#pngenjson").hide();
	});
	$.getJSON( "http://hermeshomolog:4768/api/1.0/applications", function( data ) {
		$.each( data, function( i, item ) {
			var applicationId = item.id;
			var applicationName = item.name;
			$("#leftcol").append('<p class="selitem" onclick="loadAppCodes(this,\'' + applicationId + '\')">' + applicationName + '</p>');
			application[applicationName] = applicationId;
		});
		$("#rightcol").height($("#leftcol").height());
	});
});
