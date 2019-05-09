package com.company;

        import org.apache.poi.ss.usermodel.Cell;
        import org.apache.poi.ss.usermodel.Row;
        import org.apache.poi.ss.usermodel.Sheet;
        import org.apache.poi.ss.usermodel.Workbook;
        import org.apache.poi.xssf.usermodel.XSSFWorkbook;

        import java.io.*;
        import java.net.URL;
        import java.net.URLConnection;
        import java.time.LocalDateTime;
        import java.time.format.DateTimeFormatter;
        import java.util.*;
        import java.util.concurrent.TimeUnit;

class DownloadPage {

    public static void main(String[] args) throws IOException, InterruptedException {
        String searchPerimeter = "<span class=\"h2 text-semi-bold details-panel-item--price__value\" data-currency-value>";

        Map<String, Double> cryptoPrices = new LinkedHashMap<>();

        String[] cryptoNames = new String[]{  // the name must match the name in the URL in coinmarketcap
                "nem","ripple","smartcash","cardano","verge",
                "dogecoin","reddcoin","cappasity","digibyte","life",
                "storm","tron","siacoin"
        };


        for (int i = 0; i < cryptoNames.length; i++) findCrypto(searchPerimeter,cryptoPrices,cryptoNames[i]);

        final String FILE_NAME = "./Currency - Copy.xlsx";
        FileInputStream excelInputStream = new FileInputStream(new File(FILE_NAME));
        Workbook workbook = new XSSFWorkbook(excelInputStream);
        Sheet sheet = workbook.getSheetAt(0);

        Row row = null;
        Cell cell = null;
        short columnE = 4;
        for (int i = 1; i <= cryptoNames.length; i++) {
            row=sheet.getRow(i);
            cell = row.getCell(4);

            cell.setCellValue(cryptoPrices.get(cryptoNames[i-1]));

        }

        row=sheet.getRow(cryptoNames.length+3);
        cell = row.getCell(4);
        LocalDateTime dateTime = LocalDateTime.now();
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy HH:mm:ss");
        cell.setCellValue(dateTime.format(formatter));

        excelInputStream.close();
        FileOutputStream outputStream = new FileOutputStream(FILE_NAME);
        workbook.write(outputStream);
        outputStream.close();
        workbook.close();

        System.out.println(cryptoPrices);
    }

    public static void findCrypto(String searchPerimeter, Map<String, Double> cryptoPrices, String cryptoName) throws IOException, InterruptedException {
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

        TimeUnit.SECONDS.sleep(5); // TODO: find a better way to scrape the site (HTTP 429)

        for (String s : test) {
            if (s.contains(searchPerimeter)) {
                double price = cutSB(s);
                cryptoPrices.putIfAbsent(cryptoName,price);
                break;
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
