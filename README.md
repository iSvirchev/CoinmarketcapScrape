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
        URL url = new URL("https://coinmarketcap.com/");

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

        Map<String, Double> cryptoPrices = new LinkedHashMap<>();

        for (String s : test) {
            if (s.contains("<a href=\"/currencies/bitcoin/#markets\" class=\"price\" data-usd=")) {
                double price = cutSB(s);
                cryptoPrices.putIfAbsent("Bitcoin",price);
            }
            else if (s.contains("<a href=\"/currencies/ethereum/#markets\" class=\"price\" data-usd=")) {
                double price = cutSB(s);
                cryptoPrices.putIfAbsent("Etherium",price);
            }
            else if (s.contains("<a href=\"/currencies/ripple/#markets\" class=\"price\" data-usd=")) {
                double price = cutSB(s);
                cryptoPrices.putIfAbsent("Ripple",price);
            }
            else if (s.contains("<a href=\"/currencies/bitcoin-cash/#markets\" class=\"price\" data-usd=")) {
                double price = cutSB(s);
                cryptoPrices.putIfAbsent("Bitcoin Cash",price);
            }
            else if (s.contains("<a href=\"/currencies/litecoin/#markets\" class=\"price\" data-usd=")) {
                double price = cutSB(s);
                cryptoPrices.putIfAbsent("Litecoin",price);
            }
        }
        System.out.println(cryptoPrices);
    }

    public static double cutSB(String s) {
        StringBuilder sb = new StringBuilder();
        sb.append(s);
        int indexToCutAt = 0;
        for (int i = 0; i < sb.length(); i++) {
            if(sb.charAt(i)=='>') {
                indexToCutAt = i+1;
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
