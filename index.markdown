---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>User Info Form</title>
</head>
<body>
    <h1>User Information Form</h1>
    <form id="userForm">
        <label for="name">Name:</label><br>
        <input type="text" id="name" name="name" required><br><br>
        <label for="email">Email:</label><br>
        <input type="text" id="email" name="email" required><br><br>
        <label for="classYear">classYear:</label><br>
        <input type="text" id="classYear" name="classYear" required><br><br>
        <label for="status">status:</label><br>
        <input type="text" id="status" name="status"><br><br>
        <label for="contact">contact:</label><br>
        <input type="text" id="contact" name="contact"><br><br>
        <label for="bio">bio:</label><br>
        <textarea id="bio" name="bio"></textarea><br><br>
        <label for="references">references:</label><br>
        <textarea id="references" name="references"></textarea><br><br>
        <label for="dp">Upload DP:</label><br>
        <input name="dp" id="dp" type="file"><br><br>
        <label for="reffiles">Upload Reference files:</label><br>
        <input name="reffiles" id="reffiles" type="file" multiple><br><br>
        <button type="submit">Submit</button>
    </form>
    <div id="response"></div>
    <script>
        const scriptURL = 'https://script.google.com/macros/s/AKfycby3_GuX2Pl-xQiN4xcnJy3LXi5ED_nCKCt7TGivY43q8xyVOTQLG5dgYfWP0aVtYwzpSQ/exec'
        const form = document.forms['userForm']
        form.addEventListener('submit', e => {
            e.preventDefault()
            const formD = new FormData(form)
            formD.append('timestamp', Date.now())
            // const dpdriveurl = '';
            const dpfile = document.getElementById('dp').files[0];
            if (dpfile){
                const fr = new FileReader();
                fr.readAsArrayBuffer(dpfile);
                fr.onload = f => {
                    let dpdata;
                    const url = 'https://script.google.com/macros/s/AKfycbwn1V6hm23Mgw5PD2K8t2XYGE-1w3gxvhBwv6DtGR2MYWhopfo36LPeDlRTpm0If1i1/exec';  // <--- Please set the URL of Web Apps.
                    const qs = new URLSearchParams({filename: dpfile.value || dpfile.name, mimeType: dpfile.type});
                    const dpresponse = fetch(`${url}?${qs}`, {method: "POST", body: JSON.stringify([...new Int8Array(f.target.result)])});
                    // .then(response => {dpdata = response.json()})
                    // .catch(err => console.log(err));
                    // .then(dres => res.json())
                    console.log(dpresponse.json());
                    // const dpdata = dpresponse.json();
                    formD.append('dpUrl', dpdata.fileUrl)
                }
            }
            const refFiles = document.getElementById('reffiles').files;
            if (refFiles){
                const refurl = 'https://script.google.com/macros/s/AKfycbzQACNRnkLniVhw6ch4dVx9TssXH_nqoMfJxez61dprmKoXEZIYWRIBZAp_LjZZ3mWB-w/exec';
                const refFilesURLS = [];
                for (var i = 0; i < refFiles.length; i++) {
                    const refFile = refFiles[i];
                    const rfr = new FileReader();
                    rfr.readAsArrayBuffer(refFile);
                    rfr.onload = rf => {
                        let refdata;
                        const rqs = new URLSearchParams({filename: refFile.value || refFile.name, mimeType: refFile.type});
                        fetch(`${refurl}?${rqs}`, {method: "POST", body: JSON.stringify([...new Int8Array(rf.target.result)])})
                        .then(response => {refdata = response.json()})
                        .catch(err => console.log(err));
                        // .then(rres => res.json())
                        // const refdata = refresponse.json();
                        console.log(refdata);
                        refFilesURLS.push(refdata.fileUrl)
                    }
                }
                formD.append('refUrls', refFilesURLS.join(", "));
            }
            fetch(scriptURL, { method: 'POST', body: formD})
            .then(response => console.log('Success!', response))
            .catch(error => console.error('Error!', error.message))
            // window.alert(formD)
        })
    </script>

</body>
</html>
