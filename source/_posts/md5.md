<link rel="stylesheet" type="text/css" href="/resource/css/bootstrap-theme.min.css" />
<link rel="stylesheet" type="text/css" href="/resource/css/bootstrap.min.css" />

<div class="container-fluid">
    <div class="row">
        <div class="col-md-12 col-sm-12" style="margin-top: 200px;">
            <div class="input-group input-group-lg impopover"><span class="input-group-addon" style="cursor: pointer;">MD5</span><input type="text" id="md5" class="form-control" placeholder="Original md5 string here" aria-describedby="sizing-addon" autofocus></div>
            <p class="text-center text-success" id="showMDF" style="margin-top: 15px;"></p>
        </div>
    </div>

<div class="Snow">
    <canvas id="Snow" style="position: fixed;top: 0;left: 0;width: 100%;height: 100%;z-index: 99999;background: rgba(125,137,95,0.1);pointer-events: none;"></canvas>
    <script src="/resource/js/snow.js"></script>
</div>

</div>


<!--[if lt IE 9]>
    <script src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js"></script>
    <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
<![endif]-->

<script src="/resource/js/jquery.min.js"></script>
<script src="/resource/js/md5.min.js"></script>
<script src="/resource/js/canvas-nest.min.js"></script>
<script src="/resource/js/moment.min.js"></script>

<script type="text/javascript">
    (function() {
        var lTS = +new Date(),
            sendReq = null,
            nowDate = moment().format('YYYYMMDD');
        document.getElementById("md5").addEventListener('keyup', function() {
            var showResObj = document.getElementById("showMDF");
            if (sendReq) {
                lTS = +new Date();
                sendReq && clearTimeout(sendReq);
            }
            showResObj.innerText = '';
            var str = this.value;
            sendReq = setTimeout(function() {
                showResObj.innerText = str.replace(/\s/g, '') === '' ? '' : md5(str);
            }, 800);
        })
        document.getElementById("showMDF").innerText = md5("iepngs" + nowDate);
    })()
</script><link rel="stylesheet" type="text/css" href="/resource/css/bootstrap-theme.min.css" />
<link rel="stylesheet" type="text/css" href="/resource/css/bootstrap.min.css" />

<div class="container-fluid">
    <div class="row">
        <div class="col-md-12 col-sm-12" style="margin-top: 200px;">
            <div class="input-group input-group-lg impopover"><span class="input-group-addon" style="cursor: pointer;">MD5</span><input type="text" id="md5" class="form-control" placeholder="Original md5 string here" aria-describedby="sizing-addon" autofocus></div>
            <p class="text-center text-success" id="showMDF" style="margin-top: 15px;"></p>
        </div>
    </div>

<div class="Snow">
    <canvas id="Snow" style="position: fixed;top: 0;left: 0;width: 100%;height: 100%;z-index: 99999;background: rgba(125,137,95,0.1);pointer-events: none;"></canvas>
    <script src="/resource/js/snow.js"></script>
</div>

</div>


<!--[if lt IE 9]>
    <script src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js"></script>
    <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
<![endif]-->

<script src="/resource/js/jquery.min.js"></script>
<script src="/resource/js/md5.min.js"></script>
<script src="/resource/js/canvas-nest.min.js"></script>
<script src="/resource/js/moment.min.js"></script>

<script type="text/javascript">
    (function() {
        var lTS = +new Date(),
            sendReq = null,
            nowDate = moment().format('YYYYMMDD');
        document.getElementById("md5").addEventListener('keyup', function() {
            var showResObj = document.getElementById("showMDF");
            if (sendReq) {
                lTS = +new Date();
                sendReq && clearTimeout(sendReq);
            }
            showResObj.innerText = '';
            var str = this.value;
            sendReq = setTimeout(function() {
                showResObj.innerText = str.replace(/\s/g, '') === '' ? '' : md5(str);
            }, 800);
        })
        document.getElementById("showMDF").innerText = md5("iepngs" + nowDate);
    })()
</script>