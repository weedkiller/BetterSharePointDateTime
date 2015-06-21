var BetterSharePointDate = function() {
	function ImproveDateTextBoxes() {
		$("input[id$='DateTimeField_DateTimeFieldDate']").each(function() {
			ImproveDate($(this));
		});
	}

	function ImproveDate(obj) {
		if (obj.hasClass("ImprovedDate"))
			return;

		obj.addClass("ImprovedDate");

		obj.focusout(function() {
			SanatizeDateField(obj);
		});

		obj.focus(function() {
			obj.select();
		});
	}

	function SanatizeDateField(obj) {
		if (IsDateFieldFormattedForSharePoint(obj)) {
			CleanValidDate(obj);
		} else {
			InterpretDate(obj);
		}
	}

	function IsDateFieldFormattedForSharePoint(obj) {
		var objStr = obj.val();
		objStr = objStr.replace("-", "/");

		return obj.val().match(/^[01]?\d\/[0123]?\d(\/[0-9]{0,2}\d\d)?$/);
	}

	function CleanValidDate(obj) {
		var objStr = obj.val();
		var newDate = CreateNewDate();
		objStr = objStr.replace("-", "/");

		var tokens = clean(objStr.split("/"), "");

		if (tokens.length > 1) {
			newDate.month = tokens[0] - 1;
			newDate.day = tokens[1];
			newDate.valid = true;
		}
		if (tokens.length > 2)
			newDate.year = tokens[2];

		if (newDate.valid) {
			var dateStr = GetDateString(newDate);
			obj.val(dateStr);
		}
	}

	function InterpretDate(obj) {
		var objStr = obj.val().toLowerCase();
		var newDate = CreateNewDate();

		objStr = objStr.replace(",", " "); // Commas are easier to parse as
		// spaces
		objStr = objStr.replace("1st", "1").replace("2nd", "2").replace("3rd",
				"3").replace("th ", " "); // Turns things like 1st into 1

		var tokens = clean(objStr.split(" "), "");

		for (var i = 0; i < tokens.length; i++) {
			if (isInteger(tokens[i])) {
				var num = parseInt(tokens[i]);
				if (newDate.month != null) { // if a month has been
					// specified, then this number
					// is probably a day or year
					if (newDate.day == null)
						CheckForDay(num, newDate);
					else if (newDate.year == null)
						newDate.year = CheckForYear(num);
				} else { // Could be a modifier to a relative date
					newDate.modifier = num;
				}
			} else { // if not a number
				if (!CheckForMonth(tokens[i], newDate))
					CheckForWords(tokens[i], newDate);
			}
		}

		if (newDate.valid) {
			var dateStr = GetDateString(newDate);
			obj.val(dateStr);
		}
	}

	function CheckForWords(token, newDate) {
		var d = new Date();
		if (newDate.month == null) { // Check for relative words
			switch (token) {
			case "none":
				newDate.blank = true;
				newDate.valid = true;
			case "today":
			case "tod":
			case "now":
				SetNewDate(newDate, d);
				newDate.valid = true;
				break;
			case "tom":
			case "tomorrow":
				SetNewDate(newDate, d);
				newDate.day++;
				newDate.valid = true;
				break;
			case "yest":
			case "yesterday":
				SetNewDate(newDate, d);
				newDate.day--;
				newDate.valid = true;
				break;
			case "a":
				newDAte.modifier = 1;
				break;
			case "next":
				newDate.modifier = 2;
				break;
			case "last":
				newDate.modifier = -1;
				break;
			case "sun":
			case "sunday":
			case "sundays":
				SetDayOfWeek(newDate, 0);
				break;
			case "mon":
			case "monday":
			case "mondays":
				SetDayOfWeek(newDate, 1);
				break;
			case "tue":
			case "tues":
			case "tuesday":
			case "tuesdays":
				SetDayOfWeek(newDate, 2);
				break;
			case "wed":
			case "wednesday":
			case "wednesdays":
				SetDayOfWeek(newDate, 3);
				break;
			case "th":
			case "thur":
			case "thurs":
			case "thursday":
			case "thursdays":
				SetDayOfWeek(newDate, 4);
				break;
			case "fri":
			case "friday":
			case "fridays":
				SetDayOfWeek(newDate, 5);
				break;
			case "sat":
			case "saturday":
			case "saturdays":
				SetDayOfWeek(newDate, 6);
				break;
			case "day":
			case "days":
				newDate.day = d.getDate() + newDate.modifier;
				newDate.valid = true;
				break;
			case "week":
			case "weeks":
				newDate.day = d.getDate() + (7 * newDate.modifier);
				newDate.valid = true;
				break;
			case "mo":
			case "month":
			case "months":
				newDate.month = d.getMonth() + newDate.modifier;
				newDate.valid = true;
				break;
			case "year":
			case "years":
				newDate.year = d.getFullYear() + newDate.modifier;
				newDate.valid = true;
				break;
			}
		}
	}

	function CreateNewDate() {
		var newDate = new Object();
		newDate.year = null;
		newDate.month = null;
		newDate.day = null;
		newDate.modifier = 1;
		newDate.valid = false;
		newDate.blank = false;

		return newDate;
	}

	function SetNewDate(newDate, date) {
		newDate.month = date.getMonth();
		newDate.year = date.getFullYear();
		newDate.day = date.getDate();
	}

	function GetDateString(newDate) {
		if (newDate.blank)
			return "";

		var date = new Date();
		if (newDate.year != null)
			date.setFullYear(newDate.year);
		if (newDate.day != null)
			date.setDate(newDate.day);
		if (newDate.month != null)
			date.setMonth(newDate.month);

		return "" + (date.getMonth() + 1) + "/" + date.getDate() + "/"
				+ date.getFullYear();

	}

	function SetDayOfWeek(newDate, dayOfWeek) {
		var direction = 1;
		var counter = newDate.modifier;
		var date = new Date();

		if (newDate.modifier < 0) {
			direction = -1;
			counter = -counter;
		}

		while (counter > 0) {
			date.setDate(date.getDate() + direction);
			if (date.getDay() == dayOfWeek)
				counter--;
		}

		SetNewDate(newDate, date);
		newDate.valid = true;
	}

	function CheckForMonth(token, newDate) {
		var month;
		switch (token) {
		case "jan":
		case "january":
			month = 0;
			break;
		case "feb":
		case "february":
			month = 1;
			break;
		case "mar":
		case "march":
			month = 2;
			break;
		case "apr":
		case "april":
			month = 3;
			break;
		case "may":
			month = 4;
			break;
		case "jun":
		case "june":
			month = 5;
			break;
		case "jul":
		case "july":
			month = 6;
			break;
		case "aug":
		case "august":
			month = 7;
			break;
		case "sep":
		case "sept":
		case "september":
			month = 8;
			break;
		case "oct":
		case "october":
			month = 9;
			break;
		case "nov":
		case "november":
			month = 10;
			break;
		case "dec":
		case "december":
			month = 11;
			break;
		default:
			month = null;
			break;
		}

		if (month != null) {
			newDate.month = month;
			if (month < (new Date()).getMonth()) {
				if (newDate.Year == null) // If the month comes before the
					// month we're in, assume we mean
					// the following year.
					newDate.Year = (new Date()).getFullYear() + 1;
			}

			newDate.valid = true;
			return true;
		} else
			return false;
	}

	function CheckForDay(num, newDate) {
		var MaxDaysInMonth = 31;
		if (newDate.month != null) {
			MaxDaysInMonth = new Date(2008, newDate.month + 1, 0).getDate();
		}

		if (num <= MaxDaysInMonth) {
			newDate.day = num;
			newDate.valid = true;
			return true;
		} else {
			newDate.valid = false;
			return false;
		}
	}

	function CheckForYear(num) {
		var date = new Date();
		var dateMask = date.getFullYear() - (date.getFullYear() % 100);

		if (num < 100) {
			return num + dateMask;
		} else if ((num > dateMask) && (num < (dateMask + 100))) {
			return num;
		} else
			return null;
	}

	function ImproveTimeDropDowns() {
		$("select[id$='DateTimeField_DateTimeFieldDateHours']")
				.each(
						function() {
							var HourChoose = $(this);
							if (HourChoose.hasClass("ImprovedTime"))
								return;

							HourChoose.addClass("ImprovedTime");

							// if (HourChoose.is(":visible")) {
							var MinuteChoose = $(this)
									.siblings(
											"select[id$='DateTimeField_DateTimeFieldDateMinutes']")
							var TimeDropdowns = new Object();
							TimeDropdowns.Hour = HourChoose;
							TimeDropdowns.Minute = MinuteChoose;
							HideOldTimeDropdowns(TimeDropdowns);

							var BetterTimeTextBox = CreateBetterTimeField(TimeDropdowns);

							WireUpEvents(TimeDropdowns, BetterTimeTextBox);
							// }
						});
	}

	function CreateBetterTimeField(TimeDropdowns) {
		var id = TimeDropdowns.Hour.attr('id') + '_BetterTime'

		var newTextBoxDiv = $('<span></span>').attr("id", 'TextBoxDiv' + id);

		newTextBoxDiv.html('<input type="text" class="ms-input" name="textbox'
				+ id + '" id="' + id + '" value="" >');

		newTextBoxDiv.prependTo(TimeDropdowns.Minute.parent());

		return $("input[id='" + id + "']");
	}

	function WireUpEvents(TimeDropdowns, BetterTimeTextBox) {
		CopyDropdownsToBetterTextBox(TimeDropdowns, BetterTimeTextBox);
		SetUpEventsOnBetterTextBox(BetterTimeTextBox, TimeDropdowns);
	}

	function CopyDropdownsToBetterTextBox(TimeDropdowns, BetterTimeTextBox) {
		var newTime = CreateNewTime();
		SetNewTime(newTime, TimeDropdowns.Hour.val(), TimeDropdowns.Minute
				.val());
		SetBetterTextBox(newTime, BetterTimeTextBox, TimeDropdowns);
	}

	function SetUpEventsOnBetterTextBox(BetterTimeTextBox, TimeDropdowns) {
		BetterTimeTextBox.focusout(function() {
			SanatizeBetterTimeField(BetterTimeTextBox, TimeDropdowns);
		});

		BetterTimeTextBox.focus(function() {
			if (BetterTimeTextBox.val() == "12:00 AM")
				BetterTimeTextBox.val("");
			else
				BetterTimeTextBox.select();

			if (typeof jQuery.ui.autocomplete != "undefined") {
				BetterTimeTextBox.autocomplete('search', '');
			}
		});

		var availableTags = [ "7:00 AM", "7:30 AM", "8:00 AM", "8:30 AM",
				"9:00 AM", "9:30 AM", "10:00 AM", "10:30 AM", "11:00 AM",
				"11:30 AM", "12:00 PM", "12:30 PM", "1:00 PM", "1:30 PM",
				"2:00 PM", "2:30 PM", "3:00 PM", "3:30 PM", "4:00 PM",
				"4:30 PM", "5:00 PM", "5:30 PM", "6:00 PM", "6:30 PM",
				"7:00 PM", "7:30 PM", "8:00 PM", "8:30 PM", "9:00 PM",
				"9:30 PM", "10:00 PM", "10:30 PM", "11:00 PM", "11:30 PM",
				"12:00 AM", "12:30 AM", "1:00 AM", "1:30 AM", "2:00 AM",
				"2:30 AM", "3:00 AM", "3:30 AM", "4:00 AM", "4:30 AM",
				"5:00 AM", "5:30 AM", "6:00 AM", "6:30 AM" ];

		if (typeof jQuery.ui.autocomplete != "undefined") {
			BetterTimeTextBox.autocomplete({
				source : availableTags,
				minLength : 0
			});
		}
	}

	function SanatizeBetterTimeField(obj, TimeDropdowns) {
		var objStr = obj.val().toLowerCase();
		var newTime;

		// Get string to the format "1000a" or "1000"
		objStr = objStr.replace(".", "").replace(":", "").replace(" ", "")
				.replace("am", "a").replace("pm", "p");

		var regex = /^(\d?\d)(\d\d)?([ap])?$/;
		var match = objStr.match(regex);

		if (match == null) {
			newTime = ParseAlternativeTimes(obj);
		} else {
			newTime = CreateNewTime();

			newTime.hour = parseInt(match[1]);
			if (typeof match[2] == 'undefined' || match[2] == "")
				newTime.minute = 0;
			else
				newTime.minute = parseInt(match[2]);
			if (match[3] == "")
				newTime.pm = null;
			else if (match[3] == "p")
				newTime.pm = true;
			else
				newTime.pm = false;

			newTime.valid = true;
		}

		SetBetterTextBox(newTime, obj, TimeDropdowns);
	}

	function HideOldTimeDropdowns(TimeDropdowns) {
		TimeDropdowns.Hour.hide("slow");
		TimeDropdowns.Minute.hide("slow");
	}

	function ParseAlternativeTimes(obj) {
		var newTime = CreateNewTime();
		var objStr = obj.val().toLowerCase();
		objStr = objStr.replace("in ", " ").replace("an", "1").trim();
		var tokens = objStr.split(" ");

		if (tokens.length == 1) {
			switch (tokens[0]) {
			case "noon":
			case "afternoon":
			case "lunch":
			case "lunchtime":
				newTime.hour = 12;
				newTime.pm = true;
				newTime.valid = true;
				break;
			case "midnight":
				newTime.hour = 12;
				newTime.pm = false;
				newTime.valid = true;
				break;
			case "now":
				var d = new Date();
				newTime.hour = d.getHours();
				newTime.minute = d.getMinutes();
				newTime.valid = true;
				break;
			case "cob":
				newTime.hour = 5;
				newTime.pm = true;
				newTime.valid = true;
				break;
			default:
				newTime.valid = false;
			}
		} else {
			var modifier = null;
			var word = null;
			for (var i = 0; i < tokens.length; i++) {
				if (isInteger(tokens[i])) {
					modifier = parseInt(tokens[i]);
				} else if (tokens[i] == "ago") {
					if (modifier != null)
						modifier = -modifier;
				} else {
					word = tokens[i];
				}
			}

			if (modifier == null)
				modifier = 1;

			if (word != null) {
				var d = new Date();

				switch (word) {
				case "hour":
				case "hours":
					newTime.hour = d.getHours() + modifier;
					newTime.minute = d.getMinutes();
					newTime.pm = false;
					newTime.valid = true;
					break;
				case "minute":
				case "minutes":
				case "min":
				case "mins":
					newTime.hour = d.getHours();
					newTime.minute = d.getMinutes() + modifier;
					newTime.pm = false;
					newTime.valid = true;
					break;
				}
			} else {
				newDate.valid = false;
			}
		}

		return newTime;
	}

	function CreateNewTime() {
		var newTime = new Object;

		newTime.hour = null;
		newTime.minute = null;
		newTime.pm = null;
		newTime.blank = false;
		newTime.valid = false;

		return newTime;
	}

	function SetNewTime(NewTime, HourStr, MinuteStr) {
		var tokens = HourStr.split(" ");
		if (tokens.length == 2) {
			NewTime.hour = parseInt(tokens[0]);
			if (tokens[1] == "PM")
				NewTime.pm = true;
			else
				NewTime.pm = false;
		}

		NewTime.minute = parseInt(MinuteStr);
		NewTime.valid = true;
	}

	function SetBetterTextBox(NewTime, BetterTimeTextBox, TimeDropdowns) {
		if (NewTime.blank || !NewTime.valid) {
			NewTime.hour = 12;
			NewTime.minute = 00;
			NewTime.pm = false;
			NewTime.blank = false;
			NewTime.valid = true;
		}

		if (NewTime.hour == 0)
			NewTime.hour = 24;

		if (NewTime.minute == null)
			NewTime.minute = 0;

		while (NewTime.minute >= 60) { // If there are more than 60 minutes,
			// carry the 1 to the hour and set the
			// minutes back
			NewTime.hour++;
			NewTime.minute -= 60;
		}

		NewTime.minute = NewTime.minute - (NewTime.minute % 5); // Round down to
		// 5-minute
		// increments,
		// as done by
		// SharePoint

		if (NewTime.hour > 12 && NewTime.pm != true) {
			NewTime.pm = true;
			NewTime.hour = NewTime.hour - 12;
		} else if (NewTime.hour == 12 && NewTime.pm != true) {
			NewTime.pm = true;
		}

		// Assume that if someone types an hour between 7 and 11 and didn't
		// specify am/pm, they meant am. If they type 12-6, they meant pm.
		if (NewTime.pm == null) {
			if (NewTime.hour == 12 || NewTime.hour < 7)
				NewTime.pm = true;
			else
				NewTime.pm = false;
		}

		var textStr = NewTime.hour + ":";
		if (NewTime.minute < 10)
			textStr += "0";
		textStr += NewTime.minute + " ";

		if (NewTime.pm)
			textStr += "PM";
		else
			textStr += "AM";

		BetterTimeTextBox.val(textStr);

		OldDDHourStr = NewTime.hour;
		if (NewTime.pm)
			OldDDHourStr += " PM";
		else
			OldDDHourStr += " AM";

		TimeDropdowns.Hour.val(OldDDHourStr);

		if (NewTime.minute >= 10)
			TimeDropdowns.Minute.val(NewTime.minute);
		else
			TimeDropdowns.Minute.val("0" + NewTime.minute);
	}

	function clean(value, deleteValue) {
		for (var i = 0; i < value.length; i++) {
			if (value[i] == deleteValue) {
				value.splice(i, 1);
				i--;
			}
		}
		return value;
	}

	function isInteger(n) {
		return !isNaN(parseInt(n)) && isFinite(n);
	}

	return {
		ImproveDate : ImproveDateTextBoxes,
		ImproveTime : ImproveTimeDropDowns,
		ImproveAll: function(){ ImproveDateTextBoxes(); ImproveTimeDropDowns(); }
	};
}();
