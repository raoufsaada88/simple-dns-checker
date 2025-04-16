# simple-dns-checker
a solution using JavaScript to perform a basic domain check through a public DNS API


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Domain Propagation Check</title>
</head>
<body>

    <h1>Domain Propagation Check</h1>
    <form id="domainForm">
        <label for="domain">Enter Domain:</label>
        <input type="text" id="domain" name="domain" required>
        <button type="submit">Check Domain</button>
    </form>

    <div id="result"></div>

    <script>
        document.getElementById('domainForm').addEventListener('submit', function(e) {
            e.preventDefault();
            var domain = document.getElementById('domain').value;
            var resultDiv = document.getElementById('result');
            resultDiv.innerHTML = "Checking domain...";

            // Use a public DNS API (like Google DNS) to check the domain
            fetch(`https://dns.google/resolve?name=${domain}&type=A`)
                .then(response => response.json())
                .then(data => {
                    if (data.Status === 0 && data.Answer) {
                        resultDiv.innerHTML = `Domain is propagated. IP Address: ${data.Answer[0].data}`;
                    } else {
                        resultDiv.innerHTML = "Domain is not fully propagated or not found.";
                    }
                })
                .catch(error => {
                    resultDiv.innerHTML = "Error checking domain.";
                    console.error(error);
                });
        });
    </script>

</body>
</html>


----------------------------


Explanation:
HTML Form: There's a simple form where users can enter a domain to check.

JavaScript: The form triggers a JavaScript function when submitted. This function uses the Google DNS-over-HTTPS API (https://dns.google/resolve) to query the A record of the domain.

Result Display: The result (either the IP address or a message indicating the domain isn’t propagated) is displayed dynamically on the page.

Steps:
Save this code to an HTML file and upload it to your public_html folder.

Access the HTML file via your browser (e.g., http://yourdomain.com/domain-check.html).

Enter the domain you want to check, and the script will use Google's DNS API to check its propagation.

This method relies on a public DNS service and can give a basic indication of whether the domain is propagated by checking for its A record. 
