....................................................pom.xml.............................................
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.apache.httpcomponents</groupId>
        <artifactId>httpclient</artifactId>
        <version>4.5.13</version>
    </dependency>
</dependencies>
........................
.........................................Number management service maagement.java.................................
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.HashSet;
import java.util.Set;

public class NumberManagementService {

    public static void main(String[] args) {
        try {
            int port = 8008;
            NumberManagementService service = new NumberManagementService();
            service.startServer(port);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private void startServer(int port) throws IOException {
        HttpServer server = HttpServer.create(new InetSocketAddress(port), 0);
        server.createContext("/numbers", new NumbersHandler());
        server.setExecutor(null);
        server.start();
        System.out.println("Server started on port " + port);
    }

    private class NumbersHandler implements HttpHandler {
        @Override
        public void handle(HttpExchange exchange) throws IOException {
            String query = exchange.getRequestURI().getQuery();
            String[] urls = query.split("&url=");

            Set<Integer> uniqueNumbers = new HashSet<>();

            for (String url : urls) {
                if (url.startsWith("http://") || url.startsWith("https://")) {
                    try {
                        JSONArray jsonNumbers = getJsonNumbersFromUrl(url);
                        for (int i = 0; i < jsonNumbers.length(); i++) {
                            uniqueNumbers.add(jsonNumbers.getInt(i));
                        }
                    } catch (JSONException | IOException e) {
                        // Handle the exception or log it if needed.
                    }
                }
            }

            String response = "{\"numbers\":" + uniqueNumbers.toString() + "}";
            exchange.sendResponseHeaders(200, response.length());
            OutputStream outputStream = exchange.getResponseBody();
            outputStream.write(response.getBytes());
            outputStream.close();
        }

        private JSONArray getJsonNumbersFromUrl(String url) throws JSONException, IOException {
            HttpURLConnection connection = (HttpURLConnection) new URL(url).openConnection();
            connection.setRequestMethod("GET");
            connection.setConnectTimeout(500);
            connection.setReadTimeout(500);

            int responseCode = connection.getResponseCode();
            if (responseCode == HttpURLConnection.HTTP_OK) {
                BufferedReader reader = new BufferedReader(new InputStreamReader(connection.getInputStream()));
                StringBuilder response = new StringBuilder();
                String line;
                while ((line = reader.readLine()) != null) {
                    response.append(line);
                }
                reader.close();
                return new JSONArray(response.toString());
            } else {
                // Handle the response code or throw an exception.
                return new JSONArray();
            }
        }
    }
}

..........................................................
 ...........................for running application ...........................................................
java -jar your-app-name.jar


