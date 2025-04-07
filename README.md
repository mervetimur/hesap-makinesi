# hesap-makinesi
package hesapmakinesi;

import java.util.Scanner;
import java.util.Stack;

public class Hesapmakinesi {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Stack<Double> sayilar = new Stack<>();
        Stack<Character> isaretler = new Stack<>();

        System.out.println("Sonucu görmek için '=' yazın.");

        while (true) {
            System.out.print("Bir sayı girin: ");
            String input = scanner.nextLine().trim();
            
            if (input.equalsIgnoreCase("=")) {
                break;
            }

            try {
                double sayi = Double.parseDouble(input);
                sayilar.push(sayi);
            } catch (NumberFormatException e) {
                System.out.println("Geçersiz sayı. Lütfen geçerli bir sayı girin.");
                continue;
            }

            // İşlem girişi (geçerli bir işlem girilene kadar döngü)
            char islem;
            while (true) {
                System.out.print("Bir işlem girin (+, -, *, /) veya '=': ");
                String islemInput = scanner.nextLine().trim();
                if (islemInput.equalsIgnoreCase("=")) {
                    islem = '=';
                    break;
                }
                if (islemInput.length() == 1 && "+-*/".contains(islemInput)) {
                    islem = islemInput.charAt(0);
                    break;
                }
                System.out.println("Geçersiz işlem. Lütfen +, -, *, / veya = girin.");
            }

            if (islem == '=') {
                break;
            }

            // Öncelikli işlem kontrolü (basitleştirilmiş)
            while (!isaretler.isEmpty() && oncelik(islem) <= oncelik(isaretler.peek())) {
                double sayi2 = sayilar.pop();
                double sayi1 = sayilar.pop();
                char op = isaretler.pop();
                double sonuc = islemHesapla(sayi1, sayi2, op);
                sayilar.push(sonuc);
            }
            isaretler.push(islem);
        }

        // Kalan işlemleri tamamla
        while (!isaretler.isEmpty()) {
            double sayi2 = sayilar.pop();
            double sayi1 = sayilar.pop();
            char op = isaretler.pop();
            double sonuc = islemHesapla(sayi1, sayi2, op);
            sayilar.push(sonuc);
        }

        if (!sayilar.isEmpty()) {
            System.out.println("Sonuç: " + sayilar.peek());
        } else {
            System.out.println("Hesaplanacak veri yok.");
        }

        scanner.close();
    }

    private static double islemHesapla(double sayi1, double sayi2, char islem) {
        switch (islem) {
            case '+':
                return sayi1 + sayi2;
            case '-':
                return sayi1 - sayi2;
            case '*':
                return sayi1 * sayi2;
            case '/':
                if (sayi2 != 0) {
                    return sayi1 / sayi2;
                } else {
                    throw new ArithmeticException("Bölme işlemi için ikinci sayı sıfır olamaz.");
                }
            default:
                throw new IllegalArgumentException("Geçersiz işlem: " + islem);
        }
    }

    private static int oncelik(char op) {
        switch (op) {
            case '+':
            case '-':
                return 1;
            case '*':
            case '/':
                return 2;
            default:
                return 0;
        }
    }
}
