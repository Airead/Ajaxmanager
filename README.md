#AJAX Queue/Cache/Abort/Block Manager v. 3.0
Helps you to manage AJAX requests and responses (i.e. abort requests, block requests, order requests). It is inspired by the AJAX Queue Plugin and the AjaxQueue document in the jQuery-Wiki.

$.manageAjax.create (uniqueName, options)
Creates a new ajaxmanager and returns it.

##Takes a list of options:

*	normal [jQuery-Ajax-Options](http://docs.jquery.com/Ajax/jQuery.ajax#options)
*	queue: (true|false|'clear') the queue-type specifies the queue-behaviour. The clear option clears the queue, before it adds a new ajax-task to the queue (similiar to: last in first out)
*	abortOld (true|false): aborts all "older" requests, if there is a response to a newer request
*	abortIsNoSuccess: (true|false): jQuery 1.4 calls the success-callback, if an XHR was aborted. If this option is set to true. Only the complete - callback will be called with status 'abort'.
*	abort: (function): callback, that will be called, if a XHR is aborted.
*	beforeCreate ([function]): a function that will be called, before the XHR-object is created. If you return false, the XHR won´t be created.
*	maxRequests: (number (1)) limits the number of simultaneous request in the queue. queue-option must be true or 'clear'.
*	preventDoubleRequests (true|false): prevents multiple equal requests (compares url, data and type)
*	cacheResponse (true|false): caches the response data of succesfull responses (not the xhr-object!)
*	domCompleteTrigger (false| DOM-Element, DOMNodelist, Selector or jQuery-List). Triggers the events uniqueName + "DOMComplete" and "DOMComplete" on the specified element.
*	domSuccessTrigger (false | DOM-Element, DOMNodelist, Selector or jQuery-List). Triggers the events uniqueName + "DOMSuccess" and "DOMSuccess" on the specified element.


##Your constructed ajaxmanager knows the following methods:

*	add: ([uniqueName], options) returns an id of your XHR object and takes the following options:
*	normal [jQuery-Ajax-Options](http://docs.jquery.com/Ajax/jQuery.ajax#options)
*	all additional ajaxmanager options, but not 'maxRequests' and 'queue'.
*	clear: ([uniqueName], [shouldAbort: true|false]) Clears the ajax queue of waiting requests. If the second parameter is true, all requests in proccess will be aborted, too.
*	abort: ([uniqueName], [id]) Aborts all managed XHR-requests. If you pass the optional index number of your XHR object only this XHR will be aborted.
*	getXHR: ([uniqueName], id) Returns the XHR-Object, if it is already constructed or the queue-function


##Note:

First you have to construct/configure a new Ajaxmanager

//create an ajaxmanager named someAjaxProfileName
	var someManagedAjax = $.manageAjax.create('someAjaxProfileName', { queue: true, cacheResponse: true });

You have two different ways to call your methods (don´t mix them).

Calling Ajaxmanager with uniqueName and add an ajaxrequest
	$.manageAjax.add('someAjaxProfileName', { success: function(html) { $('ul').append('<li>'+html+'</li>'); }, url: 'test.html' });

Calling Ajaxmanager with the returned ajaxmanger-Object and add an ajaxrequest with the returned object
	$.manageAjax.add({ success: function(html) { $('ul').append('<li>'+html+'</li>'); }, url: 'test.html' });

Example:
//Create an ajaxmanager named cacheQueue
	var ajaxManager = $.manageAjax.create('cacheQueue', { queue: true, cacheResponse: true }); //and add an ajaxrequest with the returned object ajaxManager.add({ success: function(html) { $('ul').append('<li>'+html+'</li>'); }, url: 'test.html' });

//Or only with the uniqueName parameter
// Generate an ajaxmanger named clearQueue
	$.manageAjax.create('clearQueue', {queue: 'clear', maxRequests: 2}); //and add an ajaxrequest with the name parameter $.manageAjax.add('clearQueue', { success: function(html) { $('ul').append('<li>'+html+'</li>'); }, url: 'test.html' });
	

// Destroys an existing Ajaxmanager. Any requests in progress are aborted and waiting requests are cleared.
	$.manageAjax.destroy (uniqueName)

##Events/Callbacks:

The ajaxmanager adds some new events or enhances some existing callbacks.

name	       									|           arguments					|				new/enhanced

beforeCreate (local)							|     XHR-ID, options					|					new

beforeSend (local)								|	XMLHttpRequest, options				|	enhanced: options arguments is passed

managerName + 'AjaxStart' (global)				|  			event						|					new

complete (local)	 							|		xhr*, status, options			|	enhanced: the options arguments is additionally passed.

managerName + 'AjaxComplete' (global)	 		|	event, xhr*, status, options		|					new

managerName + 'DOMComplete' (DOM-Event**)	 	|	event, xhr*, status, options		|					new

'DOMComplete' (DOM-Event**)	 					|	event, xhr*, status, options		|					new

success (local)	 								|	data, textStatus, xhr*, options		|	enhanced: the options arguments is additionally passed.

managerName + 'AjaxSuccess' (global)	 		|	event, xhr, options, data			|					new

managerName + 'DOMSuccess' (DOM-Event**)	 	|	event, data, options				|					new

'DOMSuccess' (DOM-Event**)	 					|	event, data, options				|					new

managerName + 'AjaxStop' (global)	 			|	event								|					new


*Note: If the cacheResponse - option is true, the xhr-argument can be an empty object.
**Note: You need to configure 'domCompleteTrigger' / 'domSuccessTrigger' to trigger these events.