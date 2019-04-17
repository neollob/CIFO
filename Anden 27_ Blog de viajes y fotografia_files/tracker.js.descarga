
if(typeof(COOTR)=='undefined'){var COOTR={__private:{eventHandlers:{}}};}

COOTR.on = function(eventName, callback)
{
    COOTR.__private.eventHandlers[eventName].callback = callback;
    return COOTR.__private[eventName];
}

COOTR.__private.trigger = function(eventName, type)
{
    if (COOTR.__private.eventHandlers[eventName]) {
        COOTR.__private.eventHandlers[eventName].handler(type);
        return true;
    }
    return false;
}

COOTR.__private.createEvent = function(eventName, handler, callback)
{
    if (!COOTR.__private.eventHandlers[eventName]) {
        COOTR.__private.eventHandlers[eventName] = {};
    }
    COOTR.__private.setHandler(eventName, handler);
    COOTR.on(eventName, callback);
    return true;
}

COOTR.__private.setHandler = function(eventName, handler)
{
    COOTR.__private.eventHandlers[eventName].handler = handler;
    return true;
}

COOTR.enableDebug = function()
{
    if (document.location.href.indexOf('ctdebug')>=0) {
        var engage = document.createElement('script');
        engage.setAttribute('type', 'text/javascript');
        engage.setAttribute('src', 
            '//'+COOTR.__private.location.hostname+'/js/debug.js?pid='
                +COOTR.__private.location.params.pid    
        );
        document.getElementsByTagName('head').item(0).appendChild(engage);
        return true;
    }
    return false;
}

COOTR.__private.handleScroll = function(type)
{

    // execute callback if exists
    COOTR.__private.eventHandlers['scroll'].callback({
        type: 'scroll',
        docpercent: COOTR.__private.scroll.docpercent
    });
    
    return true;    
}

COOTR.__private.trackScroll = function(latency)
{

    var trackScroll = function() {
        var winh = window.innerHeight || document.documentElement.clientHeight
        || document.body.clientHeight;

        var doch = Math.max( 
            document.body.scrollHeight, document.body.offsetHeight, 
            document.documentElement.clientHeight, 
            document.documentElement.scrollHeight, 
            document.documentElement.offsetHeight 
        );;

        var doctop = (window.pageYOffset || document.documentElement.scrollTop) - 
                     (document.documentElement.clientTop || 0);

                     
        var docbottom = doctop + winh;

        var mindoctop = 9999999;
        if (COOTR.__private.scroll && COOTR.__private.scroll.mindoctop!==undefined) {
            mindoctop = COOTR.__private.scroll.mindoctop;
        }
                        
        var maxdocbottom = 0;
        if (COOTR.__private.scroll && COOTR.__private.scroll.maxdocbottom!==undefined) {
            maxdocbottom = COOTR.__private.scroll.maxdocbottom;
        }
                        
        
        if (doctop < mindoctop) { mindoctop = doctop; }
        if (docbottom > maxdocbottom) { maxdocbottom = docbottom; }

        
        var docpercent = parseInt(100*(maxdocbottom-mindoctop)/doch);
        
        if ((!COOTR.__private.scroll)||(docbottom!==COOTR.__private.scroll.docbottom)) {
            COOTR.__private.scroll = {
                winh:winh, 
                doch:doch, 
                doctop:doctop, 
                docbottom:docbottom, 
                mindoctop:mindoctop, 
                maxdocbottom:maxdocbottom, 
                docpercent:docpercent
            };
            COOTR.__private.trigger('scroll');
        }
        
    }

    trackScroll();
    return setInterval(trackScroll, latency);
    
}

COOTR.__private.handleDuration = function(type)
{    
    // execute callback if exists
    COOTR.__private.eventHandlers['duration'].callback({
        type: 'duration',
        elapsedTime: COOTR.__private.duration.elapsedTime
    });
    
    return true;    
}

COOTR.__private.trackDuration = function(latency)
{
    var currentTime = new Date();
    COOTR.__private.duration = {timeStart:currentTime.getTime()};

    var trackDuration = function() {
        var currentTime = new Date();
        var duration = COOTR.__private.duration;
        duration.elapsedTime = currentTime - duration.timeStart;
        COOTR.__private.trigger('duration');
    }
    
    trackDuration();
    return setInterval(trackDuration, latency);
    
}

COOTR.__private.handleEngagement = function(type)
{
    
    var engage = document.createElement('script');
    engage.setAttribute('type', 'text/javascript');
    engage.setAttribute('src', 
        '//'+COOTR.__private.location.hostname+'/engage.js?pid='
            +COOTR.__private.location.params.pid
            +'&type='+type
    );
    document.getElementsByTagName('head').item(0).appendChild(engage);
    
    // Once triggered, disable tracking
    //COOTR.__private.untrackEngagement();
    
    // execute callback if exists
    COOTR.__private.eventHandlers['engagement'].callback({
        type: 'engagement'
    });

    
    return true;    
}

COOTR.__private.engagementConditions = function(engagement)
{

    var minReadDuration = engagement.minReadDuration;
    var elapsedTime = COOTR.__private.duration.elapsedTime;
    
    var minDocPercent = engagement.minDocPercent;
    var docpercent =  COOTR.__private.scroll.docpercent;

    return (engagement.type !== '_50_15_click') &&  (elapsedTime > minReadDuration) && (docpercent > minDocPercent);
    
}


COOTR.__private.engagementConditionsOR = function (engagement)
{

    var minReadDuration = engagement.minReadDuration;
    var elapsedTime = COOTR.__private.duration.elapsedTime;

    var minDocPercent = engagement.minDocPercent;
    var docpercent = COOTR.__private.scroll.docpercent;

    return (engagement.type === '_50_15_click') &&
            ((elapsedTime > minReadDuration)
                    || (docpercent > minDocPercent)
                    || (engagement.numClicks === engagement.userClicks));

}

COOTR.__private.trackEngagement = function (options)
{
    var trackEngagement = function (i) {
        if (COOTR.__private.engagements[i].engaged)
            return;
        if (COOTR.__private.engagementConditions(COOTR.__private.engagements[i]) || COOTR.__private.engagementConditionsOR(COOTR.__private.engagements[i])) {
            COOTR.__private.engagements[i].engaged = true;
            COOTR.__private.trigger('engagement', COOTR.__private.engagements[i].type);
        }
    };
            
    if (typeof COOTR.__private.engagements != 'object') {
        COOTR.__private.engagements = new Array();
    }
    var engagement_id = COOTR.__private.engagements.length;

    COOTR.__private.engagement = options;
    COOTR.__private.engagement.engaged = false;
    COOTR.__private.engagement.userClicks = 0;
    COOTR.__private.clicks = {count: 0};
    COOTR.__private.engagement.timers = [
        COOTR.__private.trackDuration(options.latency),
        COOTR.__private.trackScroll(options.latency),
        setInterval(function () {
            trackEngagement(engagement_id);
        }, options.latency)
    ];

    if (options.type === '_50_15_click') {
        var elBody = document.getElementsByTagName('body')[0];
        elBody.addEventListener('click', function () {
            COOTR.__private.engagements[engagement_id].userClicks++;
            COOTR.__private.clicks.count = COOTR.__private.engagements[engagement_id].userClicks;
            trackEngagement(engagement_id);
        }, true);
    }
    
    COOTR.__private.engagements[engagement_id] = COOTR.__private.engagement;
    trackEngagement(engagement_id);
}
    
COOTR.__private.untrackEngagement = function()
{
    var timers = COOTR.__private.engagement.timers;
    for (var t=0;t<timers.length;t++) {
        clearInterval(COOTR.__private.engagement.timers[t]);
    }
}

COOTR.__private.fetchRuntime = function()
{
    var tracker = document.getElementById('ctracker');
    var parseUrl = document.createElement('a');
    
    parseUrl.href = tracker.src;
   
    var params = {};
    var queries = parseUrl.search.substring(1).split('&');
    for ( i = 0; i < queries.length;i++ ) { 
        temp = queries[i].split('=');
        params[temp[0]] = temp[1];
    }    
    
    COOTR.__private.location = {
        location:parseUrl.href,
        protocol:parseUrl.protocol, 
        hostname:parseUrl.hostname, 
        port:parseUrl.port,
        pathname:parseUrl.pathname, 
        search:parseUrl.search,
        params:params,
        hash:parseUrl.hash
    };
    
}



COOTR.__private.init = function()
{
    COOTR.__private.fetchRuntime();

    COOTR.__private.createEvent('engagement',   COOTR.__private.handleEngagement, function(e) {});
    COOTR.__private.createEvent('scroll',       COOTR.__private.handleScroll, function(e) {});
    COOTR.__private.createEvent('duration',     COOTR.__private.handleDuration, function(e) {});
    
    COOTR.__private.trackEngagement({minReadDuration:60000, minDocPercent:75, latency:250, type: ''});
    COOTR.__private.trackEngagement({minReadDuration:60000, minDocPercent:50, latency:250, type: '_50_60'});
    COOTR.__private.trackEngagement({minReadDuration:30000, minDocPercent:50, latency:250, type: '_50_30'});    
    
    COOTR.__private.trackEngagement({minReadDuration:15000, minDocPercent:50, numClicks:1, latency:250, type: '_50_15_click'});    
    
    COOTR.__private.trackEngagement({minReadDuration:0, minDocPercent:75, latency:250, type: '_75_0'});    
    COOTR.__private.trackEngagement({minReadDuration:0, minDocPercent:50, latency:250, type: '_50_0'});
    
    COOTR.__private.trackEngagement({minReadDuration:60000, minDocPercent:0, latency:250, type: '_0_60'});
    COOTR.__private.trackEngagement({minReadDuration:30000, minDocPercent:0, latency:250, type: '_0_30'});
    COOTR.__private.trackEngagement({minReadDuration:15000, minDocPercent:0, latency:250, type: '_0_15'});

    COOTR.__private.trackEngagement({minReadDuration:0, minDocPercent:0, numClicks:1, latency:250, type: '_0_0_click'});    
    
    COOTR.enableDebug();
    
}

COOTR.__private.init();







