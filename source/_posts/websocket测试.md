---
title: websocket测试
date: 2019-09-04 17:24:11
tags:
---

<style>
#input-log,#output-log{border:red dashed 1px;max-height:260px;overflow-y:auto;padding:3px;line-height:28px}textarea::-webkit-scrollbar,#output-log::-webkit-scrollbar,#input-log::-webkit-scrollbar{display:none;}#input-log p,#output-log p{margin:0;border-bottom:grey dashed thin}#host-area{width:80%}#wsHost{width:88%}fieldset{border-top-width:1px;border-bottom-width:0px;border-right-width:0px;border-left-width:0px;}fieldset legend{padding-left:16px;padding-right:40px;}#ping-area{width:30%;}#ping-area input[type=text]{margin-right:10px;}#ping-second{width:100px}#msgloop-second{width:60px;margin-right: 5px}.msglog span{font-size:.8em}.redFont{color:#EE6363}.main-inner,.content-wrap{width:100% !important;margin:0;}#connecetWServer{margin: 0 5px}#blank-block{width:100;height:10px}
</style>

<div id="blank-block"></div>

<escape><!-- more --></escape>

<div style="margin: 15px"><fieldset id="host-area"><legend>连接地址</legend><label for="wsHost"></label><input type="text" placeholder="ws://127.0.0.1:8088" id="wsHost" value="ws://127.0.0.1:8088"><input type="button" value="连接" id="connecetWServer"><input type="button" value="断开" id="disConnecetWServer" disabled></fieldset><fieldset id="ping-area"><legend>PING包</legend><label for="ping-second">间隔·秒:</label><input type="text" placeholder="ping间隔" id="ping-second" value="45"><label><input type="checkbox" id="ping" disabled>开启</label></fieldset></div><div><div style="width: 45%; margin: 10px 10px 0 15px; display: block; float: left;"><textarea name="input" id="input" cols="30" rows="10" style="width: 100%" placeholder="自动保存最近一次发送的内容, 双击已发送区域内容将覆盖此处"></textarea><label for="msgloop-second">间隔秒数</label><input type="text" id="msgloop-second" value="30"><label><input type="checkbox" id="msgloop" disabled>循环发送</label><input type="button" id="send" value="发送" style="margin: 10px 0 20px 20px;"><label><br /><input type="checkbox" class="msgKeepNewer" checked>滚屏</label><input type="button" class="clearScroll" value="清屏" style="margin: 0 0 10px 20px;"><div id="input-log" data-scroll="true"></div><br /></div><div id="output-logs" style="width: 45%; margin: 10px 15px 0 30px; display: block; float: left;"><label><input type="checkbox" class="msgKeepNewer" checked>滚屏</label><input type="button" class="clearScroll" value="清屏" style="margin: 0 0 10px 20px;"><div id="output-log" style="max-height: 472px;" data-scroll="true"></div></div></div>

<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/jquery@3.2.1/dist/jquery.min.js"></script>
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/moment@2.24.0/min/moment.min.js"></script>

<script type="text/javascript">
    (function(){
        let supportWS = "WebSocket" in window;
        if (!supportWS) {
            alert("浏览器不支持WebSocket!");
            return;
        }
        
        let ws = null;
        let pingInterval = null;
        let msgloopInterval = null;
        let errorOnClose = 'WebSocket is already in CLOSING or CLOSED state.'

        let PPwsHostVal = localStorage.getItem("PPwsHostVal");
        if(PPwsHostVal){
            $("#wsHost").val(PPwsHostVal);
        }

        let PPwsContentVal = localStorage.getItem("PPwsContentVal");
        if(PPwsContentVal){
            $("textarea").val(PPwsContentVal);
        }

        let connection = function(){
            let wsHost = $.trim($("#wsHost").val());
            if(!wsHost){
                console.log("ws连接地址解析失败");
                return;
            }
            localStorage.setItem("PPwsHostVal", wsHost);
            console.log("ws连接地址: " + wsHost);
            ws = new WebSocket(wsHost);

            ws.onopen       = onopen;
            ws.onmessage    = onmessage;
            ws.onclose      = onclose;
            ws.onerror      = onerror;
        };

        let onopen = function(){
            console.log("ws server was connected")
            $("#connecetWServer").attr("disabled", true);
            $("#disConnecetWServer").attr("disabled", false);

            $("#ping, #msgloop").prop("disabled", false);
        };

        let onmessage = function (evt) {
            let receivedMsg = evt.data;
            if(receivedMsg == 'PONG'){
                return;
            }
            recordMsgLog('receive', receivedMsg);
        };

        let onclose = function () {
            recordMsgLog('error', errorOnClose);

            $("input[type='checkbox']").not(".msgKeepNewer").each(function(){
                $(this).prop('checked', false);
                $(this).parent().siblings("input, textarea").attr('disabled', false);
            });

            $("#connecetWServer").attr("disabled", false);
            $("#disConnecetWServer").attr("disabled", true);

            $("#ping, #msgloop").prop("disabled", true);
        };

        let onerror = function(evt){
            console.log(evt);
        };

        let isConnected = function(){
            try {
                return typeof ws == 'object' && ws.readyState == 1;
            } catch (error) {
                return false;
            }
        };

        $("#connecetWServer").on("click", connection);

        $("#disConnecetWServer").on("click", function(){
            $("#connecetWServer").attr("disabled", false);
            $("#disConnecetWServer").attr("disabled", true);

            ws.close();
            ws = null;
        })

        let recordMsgLog = function(position, sendMsg) {
            if(sendMsg == ''){
                return;
            }
            let renderPosiObject = '#input-log';
            let colorFontClass = '';
            if(position != 'send'){
                renderPosiObject = '#output-log';
                colorFontClass = position == 'error' ? 'redFont' : '';
            }
            sendMsg = "<span>" + moment().format('YYYY-MM-DD HH:mm:ss') + "</span><br /><p class='msglog " + colorFontClass + "'>" + sendMsg + "</p>";
            $(renderPosiObject).append(sendMsg);
            if($(renderPosiObject).data("scroll")){
                $(renderPosiObject).animate({scrollTop: $(renderPosiObject)[0].scrollHeight}, 300);
            }
        };

        $("#ping").on("change", function(){
            let pingStatus = $(this).is(':checked');
            $(this).parent()
                .siblings("input")
                .attr("disabled", pingStatus);
            if(typeof pingInterval == 'number'){
                clearInterval(pingInterval);
            }
            if(!pingStatus || !isConnected()){
                recordMsgLog('error', "PING: Off or WebSocket closed.");
                return;
            }
            recordMsgLog('error', "PING: loop started.");
            pingInterval = setInterval(function(){
                if(isConnected()){
                    ws.send("PING");
                    return;
                }
                recordMsgLog('error', "PING: " + errorOnClose);
                clearInterval(pingInterval);
            }, $("#ping-second").val() * 1000);
        })

        $("#msgloop").on("change", function(){
            let msgloopStatus = $(this).is(':checked');
            $(this).parent()
                .siblings("input")
                .not(".clearScroll")
                .attr("disabled", msgloopStatus);
            $("textarea").prop('disabled', false);
            if(typeof msgloopInterval == 'number'){
                clearInterval(msgloopInterval);
            }
            if(!msgloopStatus || !isConnected()){
                return;
            }
            let sendMsg = sendTextareaContent();
            $("textarea").prop('disabled', true);
            msgloopInterval = setInterval(function(){
                if(isConnected()){
                    ws.send(sendMsg);
                    recordMsgLog('send', sendMsg);
                    return;
                }
                recordMsgLog('error', "MSGLOOP: " + errorOnClose);
                clearInterval(msgloopInterval);
            }, $("#msgloop-second").val() * 1000);
        })

        let sendTextareaContent = function(){
            let sendMsg = $.trim($("textarea").val());
            localStorage.setItem("PPwsContentVal", sendMsg);
            if(!isConnected() || !sendMsg){
                return;
            }
            ws.send(sendMsg);
            recordMsgLog('send', sendMsg);
            return sendMsg;
        };

        $("#send").on("click", function(){
            sendTextareaContent();
        })

        $("#input-log").on("dblclick", "p", function(){
            $("#input").val($(this).text());
            $("#msgloop").click();
        })

        $(".msgKeepNewer").on("change", function(){
            let elId = $(this).parent().siblings("div:last").attr("id");
            $("#" + elId).data("scroll", $(this).is(":checked"));
        })

        $(".clearScroll").on("click", function(){
            $(this).next().empty();
        })
    })();
</script>