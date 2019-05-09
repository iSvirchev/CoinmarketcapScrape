package com.company;

        import java.io.BufferedReader;
        import java.io.IOException;
        import java.io.InputStream;
        import java.io.InputStreamReader;
        import java.net.URL;
        import java.net.URLConnection;
        import java.util.*;
        
class DownloadPage {

    public static void main(String[] args) throws IOException {
        String searchPerimeter = "<span class=\"h2 text-semi-bold details-panel-item--price__value\" data-currency-value>";

        Map<String, Double> cryptoPrices = new LinkedHashMap<>();

        findCrypto(searchPerimeter, cryptoPrices, "bitcoin"); // the name must match the name on the URL in coinmarketcap
        System.out.println(cryptoPrices);
    }

    public static void findCrypto(String searchPerimeter, Map<String, Double> cryptoPrices, String cryptoName) throws IOException {
        URL url = new URL("https://coinmarketcap.com/currencies/" + cryptoName + "/");

        // Get the input stream through URL Connection
        URLConnection con = url.openConnection();
        InputStream is = con.getInputStream();

        BufferedReader br = new BufferedReader(new InputStreamReader(is));

        String line = null;
        List<String> test = new ArrayList<>();

        // read each line and add to a List
        while ((line = br.readLine()) != null) {
            test.add(line);
        }

        for (String s : test) {
            if (s.contains(searchPerimeter)) {
                double price = cutSB(s);
                cryptoPrices.putIfAbsent(cryptoName,price);
            }
        }
    }

    public static double cutSB(String s) {
        StringBuilder sb = new StringBuilder();
        sb.append(s);
        int indexToCutAt = 0;
        for (int i = 0; i < sb.length(); i++) {
            if(sb.charAt(i)=='>') {
                indexToCutAt = i;
                break;
            }
        }
        sb.delete(0,indexToCutAt+1);
        for (int i = 0; i < sb.length(); i++) {
            if(sb.charAt(i)=='<') {
                indexToCutAt = i;
                break;
            }
        }
        sb.delete(indexToCutAt,sb.length());

        return Double.parseDouble(sb.toString());
    }
}
