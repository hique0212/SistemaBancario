# SistemaBancario
import java.util.ArrayList;
import java.util.List;

// Classe abstrata que representa uma conta
abstract class Conta {
    protected String numero;
    protected double saldo;
    protected List<String> historico;

    public Conta(String numero) {
        this.numero = numero;
        this.saldo = 0;
        this.historico = new ArrayList<>();
    }

    public void depositar(double valor) {
        if (valor > 0) {
            saldo += valor;
            historico.add("Depósito: +" + valor);
            System.out.println("Depósito de R$" + valor + " realizado na conta " + numero);
        } else {
            System.out.println("Valor inválido para depósito.");
        }
    }

    public void sacar(double valor) {
        if (valor > 0 && saldo >= valor) {
            saldo -= valor;
            historico.add("Saque: -" + valor);
            System.out.println("Saque de R$" + valor + " realizado na conta " + numero);
        } else {
            System.out.println("Saldo insuficiente ou valor inválido.");
        }
    }

    public void pix(Conta destino, double valor) {
        if (valor > 0 && saldo >= valor) {
            saldo -= valor;
            destino.saldo += valor;
            historico.add("PIX enviado: -" + valor + " para " + destino.numero);
            destino.historico.add("PIX recebido: +" + valor + " de " + this.numero);
            System.out.println("PIX de R$" + valor + " enviado para a conta " + destino.numero);
        } else {
            System.out.println("Saldo insuficiente ou valor inválido para PIX.");
        }
    }

    public void mostrarSaldo() {
        System.out.println("Saldo da conta " + numero + ": R$" + saldo);
    }

    public void mostrarHistorico() {
        System.out.println("Histórico da conta " + numero + ":");
        for (String registro : historico) {
            System.out.println(registro);
        }
    }
}

// Conta corrente
class ContaCorrente extends Conta {
    private double limiteChequeEspecial;

    public ContaCorrente(String numero, double limiteChequeEspecial) {
        super(numero);
        this.limiteChequeEspecial = limiteChequeEspecial;
    }

    @Override
    public void sacar(double valor) {
        if (valor > 0 && saldo + limiteChequeEspecial >= valor) {
            saldo -= valor;
            historico.add("Saque: -" + valor);
            System.out.println("Saque de R$" + valor + " realizado na conta corrente " + numero);
        } else {
            System.out.println("Saldo + limite insuficiente ou valor inválido.");
        }
    }
}

// Conta poupança
class ContaPoupanca extends Conta {
    public ContaPoupanca(String numero) {
        super(numero);
    }

    public void investir(double valor) {
        if (valor > 0 && saldo >= valor) {
            saldo -= valor;
            historico.add("Investimento: -" + valor);
            System.out.println("Investimento de R$" + valor + " realizado na conta poupança " + numero);
        } else {
            System.out.println("Saldo insuficiente ou valor inválido para investimento.");
        }
    }
}

// Cliente
class Cliente {
    private String nome;
    private List<Conta> contas;

    public Cliente(String nome) {
        this.nome = nome;
        this.contas = new ArrayList<>();
    }

    public void adicionarConta(Conta conta) {
        contas.add(conta);
    }

    public List<Conta> getContas() {
        return contas;
    }

    public void mostrarContas() {
        System.out.println("Contas do cliente " + nome + ":");
        for (Conta c : contas) {
            System.out.println("- " + c.numero + " | Saldo: R$" + c.saldo);
        }
    }
}

// Main
public class SistemaBancario {
    public static void main(String[] args) {
        Cliente cliente1 = new Cliente("Henrique");
        ContaCorrente cc = new ContaCorrente("001", 500);
        ContaPoupanca cp = new ContaPoupanca("002");

        cliente1.adicionarConta(cc);
        cliente1.adicionarConta(cp);

        cc.depositar(1000);
        cp.depositar(500);

        cc.sacar(200);
        cc.pix(cp, 100);

        cp.investir(200);

        cc.mostrarSaldo();
        cp.mostrarSaldo();

        cc.mostrarHistorico();
        cp.mostrarHistorico();
    }
}
