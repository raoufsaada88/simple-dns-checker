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

    <h3>DNS Records:</h3>
    <div id="result"></div>

    <script>
        document.getElementById('domainForm').addEventListener('submit', function(e) {
            e.preventDefault();
            var domain = document.getElementById('domain').value;
            var resultDiv = document.getElementById('result');
            resultDiv.innerHTML = "Checking domain...";

            // Define the record types to check
            var recordTypes = ['A', 'MX', 'CNAME', 'TXT', 'NS', 'SOA', 'AAAA'];

            var promises = recordTypes.map(function(type) {
                return fetch(`https://dns.google/resolve?name=${domain}&type=${type}`)
                    .then(response => response.json())
                    .then(data => {
                        if (data.Status === 0 && data.Answer) {
                            return { type: type, records: data.Answer };
                        } else {
                            return { type: type, records: null };
                        }
                    });
            });

            // Wait for all DNS records to be checked
            Promise.all(promises)
                .then(results => {
                    var output = '';
                    results.forEach(result => {
                        output += `<strong>${result.type} Records:</strong><br>`;
                        if (result.records) {
                            result.records.forEach(record => {
                                output += `${record.data}<br>`;
                            });
                        } else {
                            output += "No records found.<br>";
                        }
                        output += '<br>';
                    });
                    resultDiv.innerHTML = output;
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

Result Display:
The result (either the IP address or a message indicating the domain isn’t propagated) is displayed dynamically on the page.
Record Types: The script now checks for multiple DNS record types: A, MX, CNAME, TXT, NS, SOA, and AAAA.
Multiple Requests: It creates multiple fetch requests to the Google DNS API, one for each record type.
Display All Records: The results for each record type are displayed in the output.

Explanation:

Record Types: We have defined a list of record types (A, MX, CNAME, TXT, NS, SOA, AAAA), and for each record type, the script fetches the corresponding DNS records.
Promises: The fetch requests are wrapped in promises and processed using Promise.all to run them concurrently.
Result Display: After fetching all the DNS records, the results are displayed in the resultDiv. If records are found, they are listed by type; otherwise, it shows "No records found."

Steps to Use:
Save the code to an .html file.
Upload it to your public_html folder.
Access it via a browser (e.g., http://yourdomain.com/domain-check.html).

Enter the domain you want to check, and the script will show all available DNS records (A, MX, CNAME, TXT, NS, SOA, and AAAA).
