codigo
======
package Conexion;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

import javax.swing.JOptionPane;

public class Conexion{
	public Connection conexion;
	private String usuario, clave, servidor;
	private Statement s = null;
	
	public Conexion(String usu, String cla, String ser){
		
		usuario = usu;
		clave = cla;
		servidor = ser;
		
		try {
			Class.forName("com.mysql.jdbc.Driver");
		}catch(ClassNotFoundException e){
		} 
		
	}
	
	public String getUsuario(){return usuario;}
	public String getServidor(){return servidor;}
	public String getClave(){return clave;}
	public Connection getConexion(){return conexion;}
	
	public void setUsuario(String usuario){this.usuario = usuario;}
	public void setClave(String clave){this.clave = clave;}
	public void setServidor(String servidor){this.servidor = servidor;}
	public void setConexion(Connection conexion){this.conexion = conexion;}

	public Connection Conectar(){
		try {
			conexion = DriverManager.getConnection("jdbc:mysql://" + servidor, usuario, clave);
			s = conexion.createStatement();
			this.setConexion(conexion);
			return conexion;
		} catch (SQLException e){
			conexion = null;
			JOptionPane.showMessageDialog(null, "No Conectado...");
		}
		return null;
	}
	
	public boolean Abrir_Database(String name){
		if(Modificar("use " + name)){
			return true;
		}else{
			return false;
		}
	}
	
	public ResultSet Consultar(String comando){
			try {
				ResultSet rs = s.executeQuery(comando);
				int cont=0;
				while(rs.next()){
					cont++;
				}
				if(cont > 0){
				  return rs;
				}else{
				  return null;
				}
			} catch (SQLException e) {
			}
		return null;
	}
	
	public boolean Modificar(String comando){
			try {
				s.executeUpdate(comando);
				return true;
			} catch (SQLException e){
			}
		return false;
	}
	
	public boolean Guardar_Registro(String tabla, String datos){
		if(Modificar("insert into " + tabla + " values('" + datos + "')")){
			return true;	
		}else{
			return false;
		}
	}
	
	public boolean Guardar_Registro2(String tabla, String datos){
		if(Modificar("insert into " + tabla + " values(" + datos + "')")){
			return true;	
		}else{
			return false;
		}
	}
	public boolean Modificar_Registro(String tabla, String datos, String c){
		String x = "update " + tabla + " set " + datos + "' where ced='"+c+"'";
		if(Modificar(x)){
			return true;	
		}else{
			return false;
		}
	}
	public boolean Modificar_Registro2(String tabla, String datos, String c){
		String x = "update " + tabla + " set " + datos + "' where codigo_pro='"+c+"'";
		if(Modificar(x)){
			return true;	
		}else{
			return false;
		}
	}
	public boolean Modificar_Registro3(String tabla, String datos, String c){
		String x = "update " + tabla + " set " + datos + "' where nombre='"+c+"'";
		if(Modificar(x)){
			return true;	
		}else{
			return false;
		}
	}
}
package Conexion;
import Datos.Datos_Cliente;
import Conexion.Conexion;

public class Controlador_DataBase {
	
	Datos_Cliente dato;
	Conexion con;
	
	public Controlador_DataBase(Conexion conexion){
		this.con = conexion;
	}
	
	public Controlador_DataBase(Conexion conexion, Datos_Cliente dato){
		this.dato = dato;
		this.con = conexion;
	}
	
	public boolean Guardar_Registro(String tabla, String datos){
		if(con.Modificar("insert into " + tabla + " values('" + datos + "')")){
			return true;	
		}else{
			return false;
		}
	}
}
package Empleados;

import java.awt.HeadlessException;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.sql.ResultSet;
import java.sql.SQLException;

import javax.swing.JOptionPane;

public class controlador_empleados implements ActionListener, KeyListener{

	public empleados vista;
	
	public controlador_empleados(empleados xvista){
		vista = xvista;
	}

	public void actionPerformed(ActionEvent e){
		String c = e.getActionCommand();
		
		if(c.equalsIgnoreCase("volver")){
			vista.limpiar();
			vista.setVisible(false);
		}
		if(c.equalsIgnoreCase("limpiar")){
			vista.limpiar();
		}
		if(c.equalsIgnoreCase("eliminar")){
			if(vista.con.Modificar("delete from empleados where ced='" + vista.txtCedula.getText() +"'")){
				JOptionPane.showMessageDialog(null, "Registro Eliminado");
				vista.limpiar();
			}else{
				JOptionPane.showMessageDialog(null, "Registro no Encontrado...");
			}
		}
		if(c.equalsIgnoreCase("modificar")){
			if(validar()){
				String[] datos = new String[9];
				datos[0] = vista.txtCedula.getText();
				datos[1] = vista.txtNombre.getText();
				datos[2] = vista.txtApellido.getText();
				if(vista.masc.isSelected()){
					datos[3] = "Masculino";
				}else{
					datos[3] = "Femenino";
				}
				datos[4] = vista.dia.getSelectedItem().toString() + "/" +
				           vista.mes.getSelectedItem().toString() + "/" +
				           vista.año.getSelectedItem().toString();
				
				datos[5] = vista.dia2.getSelectedItem().toString() + "/" +
		           vista.mes2.getSelectedItem().toString() + "/" +
		           vista.año2.getSelectedItem().toString();
				if(vista.V.isSelected()){
					datos[6] = "Venezolano";
				}else{
					datos[6] = "Extrajenro";
				}
				datos[7] = vista.txtDireccion.getText();
				datos[8] = vista.txtTelefono.getText();
				String comando =    "nom='" + 
					                datos[1] + "',ape='" +
					                datos[2] + "',sexo='" +
					                datos[3] + "',fac_nac='" +
					                datos[4] + "',fac_ing='" +
					                datos[5] + "',nac='" +
					                datos[6] + "',dir='" +
					                datos[7] + "',telf='" +
					                datos[8];
				if(vista.con.Modificar_Registro("empleados", comando, datos[0])){
					JOptionPane.showMessageDialog(null, "Registro modificado con exito..");
					vista.limpiar();
				}else{
					JOptionPane.showMessageDialog(null, "El registro no se pudo modificar..");
				}
			}
		}
		if(c.equalsIgnoreCase("guardar")){
			if(validar()){
				String[] datos = new String[9];
				datos[0] = vista.txtCedula.getText();
				datos[1] = vista.txtNombre.getText();
				datos[2] = vista.txtApellido.getText();
				if(vista.masc.isSelected()){
					datos[3] = "Masculino";
				}else{
					datos[3] = "Femenino";
				}
				datos[4] = vista.dia.getSelectedItem().toString() + "/" +
				           vista.mes.getSelectedItem().toString() + "/" +
				           vista.año.getSelectedItem().toString();
				
				datos[5] = vista.dia2.getSelectedItem().toString() + "/" +
		           vista.mes2.getSelectedItem().toString() + "/" +
		           vista.año2.getSelectedItem().toString();
				if(vista.V.isSelected()){
					datos[6] = "Venezolano";
				}else{
					datos[6] = "Extrajenro";
				}
				datos[7] = vista.txtDireccion.getText();
				datos[8] = vista.txtTelefono.getText();
				
				String comando = datos[0] + "','" + 
                datos[1] + "','" +
                datos[2] + "','" +
                datos[3] + "','" +
                datos[4] + "','" +
                datos[5] + "','" +
                datos[6] + "','" +
                datos[7] + "','" +
                datos[8];
				if(vista.con.Guardar_Registro("empleados", comando)){
					JOptionPane.showMessageDialog(null, "registro guardado con exito..");
					vista.limpiar();
				}else{
					JOptionPane.showMessageDialog(null, "El registro no se pudo guardar..");
				}
			}
		}
	}

	public boolean validar(){
		if(vista.txtCedula.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo de la Cedula");
			return false;
		}
		if(vista.txtNombre.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo del Nombre");
			return false;
		}
		if(vista.txtApellido.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo del Apellido");
			return false;
		}
		if(vista.txtTelefono.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo del Telefono");
			return false;
		}
		if(vista.txtDireccion.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo de la Dirección");
			return false;
		}
		return true;
	}
	public void keyPressed(KeyEvent arg0){
		
	}

	public void keyReleased(KeyEvent arg0){
		ResultSet rs = vista.con.Consultar("select * from empleados where ced='" + vista.txtCedula.getText() +"'");
		if(rs != null){
			try {
				rs.first();
				vista.txtNombre.setText(rs.getObject(2).toString()); 
				vista.txtApellido.setText(rs.getObject(3).toString());
				if(rs.getObject(4).toString().equalsIgnoreCase("masculino")){
					vista.masc.setSelected(true);
				}else{
					vista.feme.setSelected(true);
				}
				String v[] = rs.getObject(5).toString().split("/"); 
				vista.dia.setSelectedIndex(Integer.parseInt(v[0])-1);
				vista.mes.setSelectedIndex(Integer.parseInt(v[1])-1);
				vista.año.setSelectedIndex(Integer.parseInt(v[2])-1920);
				
				String v2[] = rs.getObject(6).toString().split("/"); 
				vista.dia2.setSelectedIndex(Integer.parseInt(v2[0])-1);
				vista.mes2.setSelectedIndex(Integer.parseInt(v2[1])-1);
				vista.año2.setSelectedIndex(Integer.parseInt(v2[2])-1920);
				
				if(rs.getObject(7).toString().equalsIgnoreCase("venezolano")){
					vista.V.setSelected(true);
				}else{
					vista.E.setSelected(true);
				}
				vista.txtDireccion.setText(rs.getObject(8).toString());
				vista.txtTelefono.setText(rs.getObject(9).toString());
			} catch (HeadlessException e){
			} catch (SQLException e){
			}
		}else{
			vista.limpiar2();
		}
	}

	public void keyTyped(KeyEvent arg0){
		
	}

}
package Empleados;

import java.awt.Font;

import javax.swing.*;

import Conexion.Conexion;
import Panel_Generico.Panel;

public class empleados extends JPanel{

	private static final long serialVersionUID = 1L;

	public JButton btnvolver, btnmodificar, btneliminar, btnguardar,btnlimpiar;
	public Panel botones, formulario, compra, datos, factura;
	public JTextField txtNombre, txtApellido, txtCedula, txtTelefono, txtDireccion;
	public JLabel lblNombre, lblApellido, lblCedula, lblTelefono, lblDireccion, lblSexo, lblNacionalidad,
				  lblDia1 = new JLabel("Dia"), lblMes1 = new JLabel("Mes"), lblAño1 = new JLabel("Año"), lblDia2 = new JLabel("Dia"), lblMes2 = new JLabel("Mes"), lblAño2 = new JLabel("Año"),
				  lblfecha_nac = new JLabel("Fecha de Nacimiento"), lblfecha_ing = new JLabel("Fecha de Ingreso");
	public JComboBox dia = new JComboBox(), mes = new JComboBox(), año = new JComboBox();
	public JComboBox dia2 = new JComboBox(), mes2 = new JComboBox(), año2 = new JComboBox();
	public JRadioButton masc, feme, V, E;
	public ButtonGroup sexo = new ButtonGroup(), nac = new ButtonGroup();

	public Conexion con;
	 
	public empleados(int xx, int xy, int xancho, int xalto, Conexion xcon){
		con = xcon;
		this.setLayout(null);
		botones = new Panel();
		botones.setLayout(null);
		((JComponent) this).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Empleados", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(0, 0, 0)));
		
		((JComponent) botones).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Panel de Opciones", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(0, 0, 0)));
		
		formulario = new Panel(false);
		formulario.setLayout(null);
		((JComponent) formulario).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Formulario para Empleados", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(0, 0, 0)));
		
		datos = new Panel();
		datos.setLayout(null);
		((JComponent) datos).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Datos del Empleados", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(0, 0, 0)));

		
		int x=30, y=35, al= 30, an=70, an2=180;
		
		Font f = new java.awt.Font("Arial", Font.BOLD, 13);
		
		lblCedula = new JLabel("Cedula");
		lblCedula.setBounds(x, y, an, al);
		lblCedula.setFont(f);
		x += an; 
		txtCedula = new JTextField();
		txtCedula.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		lblNombre = new JLabel("Nombre");
		lblNombre.setBounds(x, y, an, al);
		lblNombre.setFont(f);
		x += an; 
		txtNombre = new JTextField();
		txtNombre.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		lblApellido = new JLabel("Apellido");
		lblApellido.setBounds(x, y, an, al);
		lblApellido.setFont(f);
		x += an; 
		txtApellido = new JTextField();
		txtApellido.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		lblTelefono = new JLabel("Telefono");
		lblTelefono.setBounds(x, y, an, al);
		lblTelefono.setFont(f);
		x += an; 
		txtTelefono = new JTextField();
		txtTelefono.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		lblDireccion = new JLabel("Dirección");
		lblDireccion.setBounds(x, y, an, al);
		lblDireccion.setFont(f);
		x += an; 
		txtDireccion = new JTextField();
		txtDireccion.setBounds(x, y, an2, al);
		x = 30; y += al+10; al = 20;
		
		lblSexo = new JLabel("Sexo");
		lblSexo.setFont(f);
		lblSexo.setBounds(x, y, an, al);
		x += an+20;
		masc = new JRadioButton("Masculino");
		masc.setFont(new java.awt.Font("Arial", 3, 13));
		masc.setBounds(x, y, 100, al);
		masc.setSelected(true);
		y += al;
		feme = new JRadioButton("Femenino");
		feme.setFont(new java.awt.Font("Arial", 3, 13));
		feme.setBounds(x, y, 100, al);
		x = 30; y += al+10;
		sexo.add(masc);
		sexo.add(feme);
		
		datos.setBounds((xancho/2)-(x+an+an2+30), 35, (x+an+an2+30)*2, y+10);
		
		x = txtNombre.getX()+txtNombre.getWidth()+25; y=35; an += 20;
		
		lblNacionalidad = new JLabel("Nacionalidad");
		lblNacionalidad.setBounds(x, y, an, al);
		lblNacionalidad.setFont(f);
		x += an;
		V = new JRadioButton("Venezolan@");
		V.setFont(new java.awt.Font("Arial", 3, 13));
		V.setBounds(x, y, 100, al);
		V.setSelected(true);
		y += al+10;
		E = new JRadioButton("Extrajer@");
		E.setFont(new java.awt.Font("Arial", 3, 13));
		E.setBounds(x, y, 100, al);
		nac.add(V);
		nac.add(E);
		
		y += al+10; x = lblNacionalidad.getX(); an = 60; al = 25;
		lblfecha_nac.setBounds(x, y, 200, 30);
		lblfecha_nac.setFont(f);
		y += 35;
		lblDia1.setBounds(x, y, 30, al); 
		lblDia1.setFont(f);
		x += 25; 
		dia.setBounds(x, y, an, al); 
		x += an+5;
		lblMes1.setBounds(x, y, 30, al); 
		lblMes1.setFont(f);
		x += 25;
		mes.setBounds(x+5, y, an, al);
		x += an+5;
		lblAño1.setBounds(x+5, y, 30, al); 
		lblAño1.setFont(f);
		x += 25;
		año.setBounds(x+10, y, an+20, al);
		
		
		// Fecha de Ingreso
		x = lblNacionalidad.getX(); y += 35;
		
		lblfecha_ing.setBounds(x, y, 200, 30);
		lblfecha_ing.setFont(f);
		y += 35;
		lblDia2.setBounds(x, y, 30, al); 
		lblDia2.setFont(f);
		x += 25; 
		dia2.setBounds(x, y, an, al); 
		x += an+5;
		lblMes2.setBounds(x, y, 30, al); 
		lblMes2.setFont(f);
		x += 25;
		mes2.setBounds(x+5, y, an, al);
		x += an+5;
		lblAño2.setBounds(x+5, y, 30, al); 
		lblAño2.setFont(f);
		x += 25;
		año2.setBounds(x+10, y, an+20, al);
		
		this.cargar();
		
		datos.add(lblfecha_nac);
		datos.add(lblfecha_ing);
		datos.add(txtCedula);
		datos.add(txtNombre);
		datos.add(txtApellido);
		datos.add(txtDireccion);
		datos.add(txtTelefono);
		datos.add(lblCedula);
		datos.add(lblNombre);
		datos.add(lblApellido);
		datos.add(lblDireccion);
		datos.add(lblTelefono);
		datos.add(lblSexo);
		datos.add(masc);
		datos.add(feme);
		datos.add(V);
		datos.add(E);
		datos.add(lblNacionalidad);
		datos.add(dia);
		datos.add(mes);
		datos.add(año);
		datos.add(dia2);
		datos.add(mes2);
		datos.add(año2);
		datos.add(lblDia1);
		datos.add(lblMes1);
		datos.add(lblAño1);
		datos.add(lblDia2);
		datos.add(lblMes2);
		datos.add(lblAño2);
		formulario.add(datos);
		
		//Panel de Botones
		btnlimpiar = new JButton("Limpiar");
		btnlimpiar.setIcon(new ImageIcon("Imagenes/vaciar.png"));
		btnmodificar = new JButton("Modificar");
		btnmodificar.setIcon(new ImageIcon("Imagenes/modificar.png"));
		btneliminar = new JButton("Eliminar");
		btneliminar.setIcon(new ImageIcon("Imagenes/borrar.png"));
		btnguardar = new JButton("Guardar");
		btnguardar.setIcon(new ImageIcon("Imagenes/guardar.png"));
		btnvolver = new JButton("Volver");
		btnvolver.setIcon(new ImageIcon("Imagenes/retornar.png"));
		
		x=40; y=25; al= 35;
		
		btnvolver.setBounds(x, y, (xancho/5)-20, al);
		x += (xancho/5)-20;
		btnlimpiar.setBounds(x, y, (xancho/5)-20, al);
		x += (xancho/5)-20;
		btnmodificar.setBounds(x, y, (xancho/5)-20, al);
		x += (xancho/5)-20;
		btneliminar.setBounds(x, y, (xancho/5)-20, al);
		x += (xancho/5)-20;
		btnguardar.setBounds(x, y, (xancho/5)-20, al);
		
		
		botones.add(btnvolver);
		botones.add(btnlimpiar);
		botones.add(btnmodificar);
		botones.add(btneliminar);
		botones.add(btnguardar);
		
		formulario.setBounds(10, 30, xancho-20, xalto-120);
		botones.setBounds(10, xalto-80, xancho-20, 73);
		this.add(formulario);
		this.add(botones);
		
		// controladores
		controlador_empleados e = new controlador_empleados(this);
		
		btnvolver.addActionListener(e);
		btnlimpiar.addActionListener(e);
		btnmodificar.addActionListener(e);
		btneliminar.addActionListener(e);
		btnguardar.addActionListener(e);
		this.txtCedula.addKeyListener(e);
		
		setBounds(xx, xy, xancho, xalto);
	}
	
	public void limpiar(){
		txtApellido.setText("");
		txtCedula.setText("");
		txtDireccion.setText("");
		txtNombre.setText("");
		txtTelefono.setText("");
		dia.setSelectedIndex(0);
		dia2.setSelectedIndex(0);
		mes.setSelectedIndex(0);
		mes2.setSelectedIndex(0);
		año.setSelectedIndex(0);
		año2.setSelectedIndex(0);
		masc.setSelected(true);
		V.setSelected(true);
	}
	
	public void limpiar2(){
		txtApellido.setText("");
		txtDireccion.setText("");
		txtNombre.setText("");
		txtTelefono.setText("");
		dia.setSelectedIndex(0);
		dia2.setSelectedIndex(0);
		mes.setSelectedIndex(0);
		mes2.setSelectedIndex(0);
		año.setSelectedIndex(0);
		año2.setSelectedIndex(0);
		masc.setSelected(true);
		V.setSelected(true);
	}
	public void cargar(){
		for(int i=1; i<32; i++){
			dia.addItem(i);
			dia2.addItem(i);
		}
		for(int i=1; i<=12; i++){
			mes.addItem(i);
			mes2.addItem(i);
		}
		for(int i=1920; i<=2011; i++){
			año.addItem(i);
			año2.addItem(i);
		}
	}
}
package Ingreso;

import java.awt.Container;
import java.awt.Dimension;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.ImageIcon;
import javax.swing.JButton;
import javax.swing.JComponent;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JPasswordField;
import javax.swing.JTextField;
import javax.swing.SwingConstants;
import Conexion.Conexion;
import Menu_Principal.Menu;

public class Ingreso extends JFrame implements ActionListener{
	
	public boolean z;
	private static final long serialVersionUID = 1L;
	
	public JFrame frame;
	public Container panelIni;
	public JLabel lblUsuario, lblContrasena, imgIni;
	public JTextField txtUsuario;
	public JPasswordField txtContrasena;
	public JButton btnAceptar, btnSalir;
	
	public Ingreso(){
		z = false;
		panelIni = new JPanel(null);
		panelIni.setLayout(null);
		((JComponent) panelIni).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Sistema de Ventas", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(0, 0, 0)));
		
		int labelAl = 20, txtAn = 200, txtAl = 27;
		
		imgIni = new JLabel();
		imgIni.setBounds(50, 50, 200, 160);
		imgIni.setIcon(new ImageIcon("Imagenes/ingreso.png"));
		panelIni.add(imgIni);
		
		lblUsuario = new JLabel("Usuario", SwingConstants.CENTER);
		lblUsuario.setFont(new java.awt.Font("Arial", 3, 14));
		lblUsuario.setBounds(50, 250, txtAn, labelAl);
		panelIni.add(lblUsuario);
		
		txtUsuario = new JTextField("root");
		txtUsuario.setFont(new java.awt.Font("Arial", 3, 12));
		txtUsuario.setBounds(50, 280, txtAn, txtAl);
		panelIni.add(txtUsuario);

		lblContrasena = new JLabel("Clave", SwingConstants.CENTER);
		lblContrasena.setFont(new java.awt.Font("Arial", 3, 14));
		lblContrasena.setBounds(50, 315, txtAn, labelAl);
		panelIni.add(lblContrasena);

		txtContrasena = new JPasswordField();
		txtContrasena.setFont(new java.awt.Font("Arial", 3, 12));
		txtContrasena.setBounds(50, 340, txtAn, txtAl);
		
		panelIni.add(txtContrasena);

		btnAceptar = new JButton("Aceptar");
		btnAceptar.setFont(new java.awt.Font("Arial", 3, 13));
		btnAceptar.setBounds(50, 390, 200, 30);
		btnAceptar.setIcon(new ImageIcon("Imagenes/entrada.png"));
		btnAceptar.addActionListener(this);
		panelIni.add(btnAceptar);

		btnSalir = new JButton("   Salir");
		btnSalir.setFont(new java.awt.Font("Arial", 3, 13));
		btnSalir.setIcon(new ImageIcon("Imagenes/salir.png"));
		btnSalir.setBounds(50, 430, 200, 30);
		panelIni.add(btnSalir);
		
		btnAceptar.setActionCommand("Aceptar");
		btnAceptar.addActionListener(this);
		
		btnSalir.setActionCommand("Salir");
		btnSalir.addActionListener(this);
		
		txtContrasena.setActionCommand("txtcontrasena");
		txtContrasena.addActionListener(this);
		
		this.setTitle("Sistema de Ventas");
		this.getContentPane().add(panelIni, null);
		this.setDefaultCloseOperation(JFrame.DO_NOTHING_ON_CLOSE); //desactiva el boton de cerrar
		this.setSize(new Dimension(300, 500));
		this.setLocationRelativeTo(null);
		this.setResizable(false);
		this.setVisible(true);
	}

	public void Abrir_DataBase(Conexion conexion){
		if(conexion.Modificar("create database charcu;")){
			JOptionPane.showMessageDialog(null, "La Base de Datos no existe...");
			JOptionPane.showMessageDialog(null, "Base de Datos Creada...");
            
		}
		conexion.Abrir_Database("charcu");
				if(conexion.Modificar("create table empleados(ced varchar(15) primary key, "+
						 									  "nom varchar(15) not null, " +
						 									  "ape varchar(15) not null, " +
						 									  "sexo varchar(15) not null, " +
						 									  "fac_nac varchar(15) not null, " +
						 									  "fac_ing varchar(15) not null, " +
						 									  "nac varchar(15) not null, " +
						 									  "dir varchar(50) not null, " +
						 									  "telf varchar(15) not null)")){
					
					JOptionPane.showMessageDialog(null, "Se creo la tabla Empleados...");
				}
				if(conexion.Modificar("create table cliente(ced varchar(15) primary key, "+
						 								   "nom varchar(15) not null, " +
						 								   "ape varchar(15) not null, " +
						 								   "dir varchar(50) not null, " +
				  										   "telf varchar(15) not null)")){
				    JOptionPane.showMessageDialog(null, "Se creo la tabla Cliente...");
				}
				if(conexion.Modificar("create table ventas(id integer primary key auto_increment," +
									                        "codigo_pro varchar(15) not null, "+
									                        "nombre_pro varchar(15) not null, "+
														   	"cantidad varchar(15) not null, " +
															"precio varchar(15) not null)")){
					JOptionPane.showMessageDialog(null, "Se creo la tabla Ventas...");
				}
				if(conexion.Modificar("create table inventario(codigo_pro varchar(15) primary key, "+
						                                      "nombre varchar(15) not null, " +
															  "precio varchar(15) not null, " +
														      "existencia varchar(15) not null)")){
				JOptionPane.showMessageDialog(null, "Se creo la tabla Inventario...");
				}
				if(conexion.Modificar("create table proveedores(nombre varchar(15) primary key, "+
														   	   "rif varchar(15) not null, " +
														   	   "telf varchar(15) not null, " +
														   	   "email varchar(15) not null, " +
															   "dir varchar(15) not null)")){
				JOptionPane.showMessageDialog(null, "Se creo la tabla Proveedores...");
				}
	}
	
	@SuppressWarnings("deprecation")
	public void actionPerformed(ActionEvent e){
		String c = e.getActionCommand();
		if(!z){
			if(c.equalsIgnoreCase("aceptar")){
				String u = txtUsuario.getText();
				String p = txtContrasena.getText();
				String s = "localhost";
				Conexion con = new Conexion(u,p,s);
				con.Conectar();
				if(con.conexion != null){
					this.Abrir_DataBase(con);
					new Menu(con);
					this.dispose();
					z = true;
				}
				
			}
			if(c.equalsIgnoreCase("salir")){
				System.exit(0);
			}
		}
	}
	
}
package Inventario;

import java.awt.HeadlessException;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.sql.ResultSet;
import java.sql.SQLException;

import javax.swing.JOptionPane;

public class controlador_inventario implements ActionListener, KeyListener{

	public inventario vista;
	
	public controlador_inventario(inventario xvista){
		vista = xvista;
	}

	public void actionPerformed(ActionEvent e){
		String c = e.getActionCommand();
		
		if(c.equalsIgnoreCase("volver")){
			vista.limpiar();
			vista.setVisible(false);
		}
		if(c.equalsIgnoreCase("limpiar")){
			vista.limpiar();
		}
		if(c.equalsIgnoreCase("guardar")){
			if(validar()){
				if(es_numero(vista.txtprecio.getText())){
					String[] datos = new String[4];
					datos[0] = vista.txtcodigo.getText();
					datos[1] = vista.txtNombre.getText();
					datos[2] = vista.txtprecio.getText();
					datos[3] = vista.txtexistencia.getText();
					String comando =    datos[0] + "','" +
						                datos[1] + "','" +
						                datos[2] + "','" +
						                datos[3];
					if(vista.con.Guardar_Registro("inventario", comando)){
						JOptionPane.showMessageDialog(null, "registro guardado con exito..");
						vista.limpiar();
					}else{
						JOptionPane.showMessageDialog(null, "El registro no se pudo guardar..");
					}
				}else{
					JOptionPane.showMessageDialog(null, "La existencia debe ser un valor númerico...");
				}
			}
		}
		if(c.equalsIgnoreCase("modificar")){
			if(validar()){
				String[] datos = new String[4];
				datos[0] = vista.txtcodigo.getText();
				datos[1] = vista.txtNombre.getText();
				datos[2] = vista.txtprecio.getText();
				datos[3] = vista.txtexistencia.getText();
				String comando =    "codigo_pro='" + 
					                datos[0] + "',nombre='" +
					                datos[1] + "',precio='" +
					                datos[2] + "',existencia='" +
					                datos[3];
				if(vista.con.Modificar_Registro2("inventario", comando, datos[0])){
					JOptionPane.showMessageDialog(null, "Registro modificado con exito..");
					vista.limpiar();
				}else{
					JOptionPane.showMessageDialog(null, "El registro no se pudo modificar..");
				}
			}
		}
		if(c.equalsIgnoreCase("eliminar")){
			if(vista.con.Modificar("delete from inventario where codigo_pro='" + vista.txtcodigo.getText() +"'")){
				JOptionPane.showMessageDialog(null, "Registro Eliminado");
				vista.limpiar();
			}else{
				JOptionPane.showMessageDialog(null, "Registro no Encontrado...");
			}
		}
	}
	
	public boolean es_numero(String c){
		try{
			Integer.parseInt(c);
			return true;
		}catch(NumberFormatException e){
			return false;
		}	
	}
	public boolean validar(){
		if(vista.txtcodigo.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo del Código");
			return false;
		}
		if(vista.txtNombre.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo del Nombre");
			return false;
		}
		if(vista.txtexistencia.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo de la Existencia");
			return false;
		}
		if(vista.txtprecio.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo del Precio");
			return false;
		}
		return true;
	}
	public void keyPressed(KeyEvent arg0){
		
	}

	public void keyReleased(KeyEvent arg0){
		ResultSet rs = vista.con.Consultar("select * from inventario where codigo_pro='" + vista.txtcodigo.getText() +"'");
		if(rs != null){
			try {
				rs.first();
				vista.txtNombre.setText(rs.getObject(2).toString()); 
				vista.txtprecio.setText(rs.getObject(3).toString());
				vista.txtexistencia.setText(rs.getObject(4).toString());
			} catch (HeadlessException e){
			} catch (SQLException e){
			}
		}
	}

	public void keyTyped(KeyEvent arg0){
		
	}

}
package Inventario;

import java.awt.Font;

import javax.swing.*;

import Conexion.Conexion;
import Panel_Generico.Panel;

public class inventario extends JPanel{

	private static final long serialVersionUID = 1L;

	public JButton btnvolver, btnmodificar, btneliminar, btnguardar,btnlimpiar;
	public Panel botones, formulario, datos;
	public JTextField txtNombre, txtcodigo, txtprecio, txtexistencia;
	public JLabel lblNombre, lblcodigo, lblprecio, lblexistencia = new JLabel("Existencia");

	public Conexion con;
	
	public inventario(int xx, int xy, int xancho, int xalto, Conexion xcon){
		con = xcon;
		this.setLayout(null);
		botones = new Panel();
		botones.setLayout(null);
		((JComponent) this).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Inventario", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(255, 255, 255)));
		
		((JComponent) botones).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Panel de Opciones", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(255, 255, 255)));
		
		formulario = new Panel(true);
		formulario.setLayout(null);
		((JComponent) formulario).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Formulario para el manejo del Inventario", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(70, 70, 70)));
		
		datos = new Panel();
		datos.setLayout(null);
		((JComponent) datos).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Datos de los productos en el Inventario", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(70, 70, 70)));
		
		int x=30, y=35, al= 30, an=150, an2=180;
		
		Font f = new java.awt.Font("Arial", Font.BOLD, 13);
		
		lblcodigo = new JLabel("código");
		lblcodigo.setBounds(x, y, an, al);
		lblcodigo.setFont(f);
		x += an2-30; 
		txtcodigo = new JTextField();
		txtcodigo.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		lblNombre = new JLabel("Nombre del Producto");
		lblNombre.setBounds(x, y, an, al);
		lblNombre.setFont(f);
		x += an2-30; 
		txtNombre = new JTextField();
		txtNombre.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		lblprecio = new JLabel("Precio");
		lblprecio.setBounds(x, y, an, al);
		lblprecio.setFont(f);
		x += an2-30; 
		txtprecio = new JTextField();
		txtprecio.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		lblexistencia = new JLabel("Existencia");
		lblexistencia.setBounds(x, y, an, al);
		lblexistencia.setFont(f);
		x += an2-30; 
		txtexistencia = new JTextField();
		txtexistencia.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		datos.setBounds((xancho/2)-(x+an+an2+30), 35, (x+an+an2+30)*2, y+10);
		datos.add(txtNombre);
		datos.add(txtcodigo);
		datos.add(txtexistencia);
		datos.add(txtprecio);
		datos.add(lblNombre);
		datos.add(lblcodigo);
		datos.add(lblexistencia);
		datos.add(lblprecio);
		formulario.add(datos);
		
		//Panel de Botones
		btnlimpiar = new JButton("Limpiar");
		btnlimpiar.setIcon(new ImageIcon("Imagenes/vaciar.png"));
		btnmodificar = new JButton("Modificar");
		btnmodificar.setIcon(new ImageIcon("Imagenes/modificar.png"));
		btneliminar = new JButton("Eliminar");
		btneliminar.setIcon(new ImageIcon("Imagenes/borrar.png"));
		btnguardar = new JButton("Guardar");
		btnguardar.setIcon(new ImageIcon("Imagenes/guardar.png"));
		btnvolver = new JButton("Volver");
		btnvolver.setIcon(new ImageIcon("Imagenes/retornar.png"));
		
		x=40; y=25; al= 35;
		
		btnvolver.setBounds(x, y, (xancho/5)-20, al);
		x += (xancho/5)-20;
		btnlimpiar.setBounds(x, y, (xancho/5)-20, al);
		x += (xancho/5)-20;
		btnmodificar.setBounds(x, y, (xancho/5)-20, al);
		x += (xancho/5)-20;
		btneliminar.setBounds(x, y, (xancho/5)-20, al);
		x += (xancho/5)-20;
		btnguardar.setBounds(x, y, (xancho/5)-20, al);
		
		
		botones.add(btnvolver);
		botones.add(btnlimpiar);
		botones.add(btnmodificar);
		botones.add(btneliminar);
		botones.add(btnguardar);
		
		formulario.setBounds(10, 30, xancho-20, xalto-120);
		botones.setBounds(10, xalto-80, xancho-20, 73);
		this.add(formulario);
		this.add(botones);
		
		// controladores
		controlador_inventario e = new controlador_inventario(this);
		
		btnvolver.addActionListener(e);
		btnlimpiar.addActionListener(e);
		btnmodificar.addActionListener(e);
		btneliminar.addActionListener(e);
		btnguardar.addActionListener(e);
		
		txtcodigo.addKeyListener(e);
		
		setBounds(xx, xy, xancho, xalto);
	}
	
	public void limpiar(){
		txtcodigo.setText("");
		txtexistencia.setText("");
		txtprecio.setText("");
		txtNombre.setText("");
	}
}
package Menu_Principal;

import java.awt.Container;
import java.awt.Dimension;
import java.awt.Toolkit;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.*;
import Panel_Generico.Panel;
import Proeedores.proveedores;
import Reportes.reportes;
import Ventas.Ventas;
import Clientes.clientes;
import Conexion.Conexion;
import Empleados.empleados;
import Inventario.inventario;

public class Menu extends JFrame implements ActionListener{
	
	private static final long serialVersionUID = 1L;
	
	public JPanel botones; 
	public Panel fondo;
	public JFrame frame;
	public Container panel1;
	public JButton btnSalir, btnEmpleados, btnClientes, btnVentas, btnInventario, btnProveedores, btnReportes;
	public JLabel imagen;
	
	public clientes pclientes;
	public empleados pempleados;
	public Ventas pventas;
	public inventario pinventario;
	public proveedores pproveedores;
	public reportes preportes;
	public Panel ima_fondo;
	
	Conexion con;

	public Menu(Conexion conexion){
		 Dimension d1 = Toolkit.getDefaultToolkit().getScreenSize();
		 con = conexion;
		 fondo = new Panel("f.jpg");
		 fondo.setLayout(null);
		 
		 botones = new JPanel(null);
		 botones.setLayout(null);
		 botones.setOpaque(false);
		((JComponent) fondo).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Sistema de Ventas", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(0, 0, 0)));
		
		((JComponent) botones).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Menu de Opciones", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(0, 0, 0)));
		
		int x=25, y=40;
		btnEmpleados = new JButton("Empleados");
		btnEmpleados.setBounds(x, y, 150, 40);
		y += 50;
		btnClientes = new JButton("Clientes");
		btnClientes.setBounds(x, y, 150, 40);
		y += 50;
		btnVentas = new JButton("Ventas");
		btnVentas.setBounds(x, y, 150, 40);
		y += 50;
		btnInventario = new JButton("Inventario");
		btnInventario.setBounds(x, y, 150, 40);
		y += 50;
		btnProveedores = new JButton("Proveedores");
		btnProveedores.setBounds(x, y, 150, 40);
		y += 50;
		btnReportes = new JButton("Reportes");
		btnReportes.setBounds(x, y, 150, 40);
		y += 50;
		btnSalir = new JButton("Salir");
		btnSalir.setBounds(x, y, 150, 40);
		y = d1.height-130;
		
		botones.setBounds(20, 40, 200, y);
		
		pclientes = new clientes(240, 40, d1.width-260, y, conexion);
		pempleados = new empleados(240, 40, d1.width-260, y, conexion);
		pventas = new Ventas(240, 40, d1.width-260, y, conexion);
		pinventario = new inventario(240, 40, d1.width-260, y, conexion);
		pproveedores = new proveedores(240, 40, d1.width-260, y, conexion);
		preportes  = new reportes(240, 40, d1.width-260, y, conexion);
		
		pclientes.setOpaque(false);
		pempleados.setOpaque(false);
		pventas.setOpaque(false);
		pinventario.setOpaque(false);
		pproveedores.setOpaque(false);
		preportes.setOpaque(false);
		
		// Visibilidad
		
		pclientes.setVisible(false);
		pempleados.setVisible(false);
		pventas.setVisible(false);
		pinventario.setVisible(false);
		pproveedores.setVisible(false);
		preportes.setVisible(false);
		
		// Add´s
		botones.add(btnEmpleados);
		botones.add(btnClientes);
		botones.add(btnVentas);
		botones.add(btnInventario);
		botones.add(btnProveedores);
		botones.add(btnReportes);
		botones.add(btnSalir);
		fondo.add(botones);
		
		fondo.add(pclientes);
		fondo.add(pempleados);
		fondo.add(pventas);
		fondo.add(pinventario);
		fondo.add(pproveedores);
		fondo.add(preportes);
		//fondo.add(ima_fondo);
		add(fondo);
		
		//Listener´s
		btnEmpleados.addActionListener(this);
		btnClientes.addActionListener(this);
		btnVentas.addActionListener(this);
		btnInventario.addActionListener(this);
		btnProveedores.addActionListener(this);
		btnReportes.addActionListener(this);
		btnSalir.addActionListener(this);
		
		btnEmpleados.setHorizontalAlignment(JButton.LEFT);
		btnVentas.setHorizontalAlignment(JButton.LEFT);
		btnClientes.setHorizontalAlignment(JButton.LEFT);
		btnReportes.setHorizontalAlignment(JButton.LEFT);
		btnInventario.setHorizontalAlignment(JButton.LEFT);
		btnProveedores.setHorizontalAlignment(JButton.LEFT);
		btnSalir.setHorizontalAlignment(JButton.LEFT);
		
		btnVentas.setIcon(new ImageIcon("Imagenes/dolar.jpg"));
		btnReportes.setIcon(new ImageIcon("Imagenes/reportes.jpg"));
		btnInventario.setIcon(new ImageIcon("Imagenes/inventario.jpg"));
		btnEmpleados.setIcon(new ImageIcon("Imagenes/docente.png"));
		btnClientes.setIcon(new ImageIcon("Imagenes/user.png"));
		btnProveedores.setIcon(new ImageIcon("Imagenes/proveedores.jpg"));
		btnSalir.setIcon(new ImageIcon("Imagenes/salir.png"));

		this.setTitle("Sistema de Ventas");
		this.setBounds(0,0, d1.width, d1.height-45);
		this.setResizable(false);
		this.setVisible(true);
		this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}

	public void actionPerformed(ActionEvent e){
		if(e.getActionCommand().equalsIgnoreCase("salir")){
			System.exit(0);
		}
		if(e.getActionCommand().equalsIgnoreCase("clientes")){
			pclientes.cargar();
			pclientes.setVisible(true);
			pempleados.setVisible(false);
			pventas.setVisible(false);
			pproveedores.setVisible(false);
			preportes.setVisible(false);
			pinventario.setVisible(false);
			
			this.repaint();
		}
		if(e.getActionCommand().equalsIgnoreCase("empleados")){
			pclientes.setVisible(false);
			pempleados.setVisible(true);
			pventas.setVisible(false);
			pproveedores.setVisible(false);
			preportes.setVisible(false);
			pinventario.setVisible(false);
			this.repaint();
		}
		if(e.getActionCommand().equalsIgnoreCase("ventas")){
			pventas.cargar();
			pclientes.setVisible(false);
			pempleados.setVisible(false);
			pventas.setVisible(true);
			pproveedores.setVisible(false);
			preportes.setVisible(false);
			pinventario.setVisible(false);
			this.repaint();
		}
		if(e.getActionCommand().equalsIgnoreCase("inventario")){
			pclientes.setVisible(false);
			pempleados.setVisible(false);
			pventas.setVisible(false);
			pproveedores.setVisible(false);
			preportes.setVisible(false);
			pinventario.setVisible(true);
			this.repaint();
		}
		if(e.getActionCommand().equalsIgnoreCase("proveedores")){
			pclientes.setVisible(false);
			pempleados.setVisible(false);
			pventas.setVisible(false);
			pinventario.setVisible(false);
			preportes.setVisible(false);
			pproveedores.setVisible(true);
			this.repaint();
		}
		if(e.getActionCommand().equalsIgnoreCase("reportes")){
			pclientes.setVisible(false);
			pempleados.setVisible(false);
			pventas.setVisible(false);
			pinventario.setVisible(false);
			pproveedores.setVisible(false);
			preportes.setVisible(true);
			this.repaint();
		}
	}
}
package PDF;

public class Margen{
	public float Superior;
	public float Inferior;
	public float Derecho;
	public float Izquierdo;
	
	public Margen(float superior, float inferior, float derecho, float izquierdo) {
		Superior = superior;
		Inferior = inferior;
		Derecho = derecho;
		Izquierdo = izquierdo;
	}
	
	
}
package PDF;

import java.io.FileNotFoundException;
import java.io.FileOutputStream;

import javax.swing.JOptionPane;

import com.itextpdf.text.Document;
import com.itextpdf.text.DocumentException;
import com.itextpdf.text.Font;
import com.itextpdf.text.Paragraph;
import com.itextpdf.text.Rectangle;
import com.itextpdf.text.pdf.PdfWriter;

public class PDF {
	private Document documento;
	private String name;
	public PDF(String nombre, Rectangle tamaño, Margen margen){
		
		documento = new Document(tamaño, margen.Superior, margen.Inferior, margen.Izquierdo, margen.Derecho);
		name = nombre;
		try {
			PdfWriter.getInstance(documento, new FileOutputStream(name + ".pdf"));
		} catch (FileNotFoundException e){
		} catch (DocumentException e){
		}
	}
	
    public boolean Escribir(Paragraph parrafo){
        try {
            documento.open();
            documento.add(parrafo);
        } catch (DocumentException de) {
            JOptionPane.showMessageDialog(null, de.getMessage());
            return false;
        }
        return true;
    }
    
    public boolean Escribir(String parrafo){
        try {
            documento.open();
            documento.add(new Paragraph(parrafo));
        } catch (DocumentException de) {
            JOptionPane.showMessageDialog(null, de.getMessage());
            return false;
        }
        return true;
    }
    
    public boolean Escribir_con_Formato(String texto, Font fuente){
        try {
            documento.open();
            Paragraph parrafo = new Paragraph(texto, fuente);
            documento.add(parrafo);
        } catch (DocumentException de) {
            JOptionPane.showMessageDialog(null, de.getMessage());
            return false;
        }
        return true;
    }
    
    public void Cerrar(){
    	documento.close();
    }
}
package Panel_Generico;


import java.awt.Color;
import java.awt.Graphics;
import java.awt.Image;
import javax.swing.ImageIcon;
import javax.swing.JPanel;

public class Panel extends JPanel {

	private static final long serialVersionUID = 1L;
	
	private Image imagen;

    public Panel(){
    	imagen = null;
    	this.setOpaque(false);
    }

    public Panel(boolean x){
    	imagen = null;
    	this.setOpaque(x);
    }
    public Panel(Color c){
    	imagen = null;
    	this.setOpaque(true);
    	this.setBackground(c);
    }
    
    public Panel(String nombreImagen) {
        if (nombreImagen != null) {
            imagen = new ImageIcon(nombreImagen).getImage();
        }
    }

    public Panel(Image imagenInicial) {
        if (imagenInicial != null) {
            imagen = imagenInicial;
        }
    }

    public void setImagen(String nombreImagen) {
        if (nombreImagen != null) {
            imagen = new ImageIcon(getClass().getResource(nombreImagen)).getImage();
        } else {
            imagen = null;
        }

        repaint();
    }

    public void setImagen(Image nuevaImagen) {
        imagen = nuevaImagen;

        repaint();
    }

    public void paint(Graphics g) {
        if (imagen != null) {
            g.drawImage(imagen, 0, 0, getWidth(), getHeight(), this);
            setOpaque(false);
        } 

        super.paint(g);
    }
}
import javax.swing.JDialog;
import javax.swing.JFrame;
import javax.swing.UIManager;

import Ingreso.Ingreso;

public class Principal {

	public static void main(String[] args) {
		try{
			JFrame.setDefaultLookAndFeelDecorated(true);
			JDialog.setDefaultLookAndFeelDecorated(true);
			UIManager.setLookAndFeel(UIManager.getSystemLookAndFeelClassName());
			UIManager.setLookAndFeel("com.sun.java.swing.plaf.nimbus.NimbusLookAndFeel");
		}catch(Exception e){}
	    new Ingreso();

	}

}
package Proeedores;

import java.awt.HeadlessException;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.sql.ResultSet;
import java.sql.SQLException;

import javax.swing.JOptionPane;

public class controlador_proveedores implements ActionListener, KeyListener{

	public proveedores vista;
	
	public controlador_proveedores(proveedores xvista){
		vista = xvista;
	}

	public void actionPerformed(ActionEvent e){
		String c = e.getActionCommand();
		
		if(c.equalsIgnoreCase("volver")){
			vista.limpiar();
			vista.setVisible(false);
		}
		if(c.equalsIgnoreCase("limpiar")){
			vista.limpiar();
		}
		if(c.equalsIgnoreCase("guardar")){
			if(validar()){
					String[] datos = new String[5];
					datos[0] = vista.txtNombre.getText();
					datos[1] = vista.txtrif.getText();
					datos[2] = vista.txttelefono.getText();
					datos[3] = vista.txtemail.getText();
					datos[4] = vista.txtdireccion.getText();
					String comando =    datos[0] + "','" +
						                datos[1] + "','" +
						                datos[2] + "','" +
						                datos[3] + "','" +
						                datos[4];
					if(vista.con.Guardar_Registro("proveedores", comando)){
						JOptionPane.showMessageDialog(null, "registro guardado con exito..");
						vista.limpiar();
					}else{
						JOptionPane.showMessageDialog(null, "El registro no se pudo guardar..");
					}
			}
		}
		if(c.equalsIgnoreCase("modificar")){
			if(validar()){
				String[] datos = new String[5];
				datos[0] = vista.txtNombre.getText();
				datos[1] = vista.txtrif.getText();
				datos[2] = vista.txttelefono.getText();
				datos[3] = vista.txtemail.getText();
				datos[4] = vista.txtdireccion.getText();
				String comando =    "nombre='" + 
					                datos[0] + "',rif='" +
					                datos[1] + "',telf='" +
					                datos[2] + "',email='" +
					                datos[3] + "',dir='" +
					                datos[4]; 
				if(vista.con.Modificar_Registro3("proveedores", comando, datos[0])){
					JOptionPane.showMessageDialog(null, "Registro modificado con exito..");
					vista.limpiar();
				}else{
					JOptionPane.showMessageDialog(null, "El registro no se pudo modificar..");
				}
			}
		}
		if(c.equalsIgnoreCase("eliminar")){
			if(vista.con.Modificar("delete from proveedores where nombre='" + vista.txtNombre.getText() +"'")){
				JOptionPane.showMessageDialog(null, "Registro Eliminado");
				vista.limpiar();
			}else{
				JOptionPane.showMessageDialog(null, "Registro no Encontrado...");
			}
		}
	}
	
	public boolean validar(){
		if(vista.txtdireccion.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo de la Dirección");
			return false;
		}
		if(vista.txtemail.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo del E-mail");
			return false;
		}
		if(vista.txtNombre.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo del Nombre");
			return false;
		}
		if(vista.txtrif.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo del R.I.F.");
			return false;
		}
		if(vista.txttelefono.getText().length() == 0){
			JOptionPane.showMessageDialog(null, "Debe llenar el campo del Telefono");
			return false;
		}
		return true;
	}
	public void keyPressed(KeyEvent arg0){
		
	}

	public void keyReleased(KeyEvent arg0){
		ResultSet rs = vista.con.Consultar("select * from proveedores where nombre='" + vista.txtNombre.getText() +"'");
		if(rs != null){
			try {
				rs.first();
				vista.txtrif.setText(rs.getObject(2).toString()); 
				vista.txttelefono.setText(rs.getObject(3).toString());
				vista.txtemail.setText(rs.getObject(4).toString());
				vista.txtdireccion.setText(rs.getObject(5).toString());
			} catch (HeadlessException e){
			} catch (SQLException e){
			}
		}else{
			vista.txtrif.setText(""); 
			vista.txttelefono.setText("");
			vista.txtemail.setText("");
			vista.txtdireccion.setText("");
		}
	}

	public void keyTyped(KeyEvent arg0){
		
	}

}
package Proeedores;

import java.awt.Font;
import javax.swing.*;

import Conexion.Conexion;
import Panel_Generico.Panel;

public class proveedores extends JPanel{

	private static final long serialVersionUID = 1L;

	public JButton btnvolver, btnmodificar, btneliminar, btnguardar,btnlimpiar;
	public Panel botones, formulario, datos;
	public JTextField txtNombre, txtrif, txttelefono, txtemail, txtdireccion, txttipo;
	public JLabel lblNombre = new JLabel("Nombre"), lblrif = new JLabel("R.I.F."), lbltelefono = new JLabel("Telefono"), lblemail = new JLabel("E-mail"), lbldireccion = new JLabel("Dirección");

	public Conexion con;
	
	public proveedores(int xx, int xy, int xancho, int xalto, Conexion xcon){
		con = xcon;
		this.setLayout(null);
		botones = new Panel();
		botones.setLayout(null);
		((JComponent) this).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Clientes", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(255, 255, 255)));
		
		((JComponent) botones).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Panel de Opciones", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(255, 255, 255)));
		
		formulario = new Panel(true);
		formulario.setLayout(null);
		((JComponent) formulario).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Formulario para el manejo del Inventario", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(70, 70, 70)));
		
		datos = new Panel();
		datos.setLayout(null);
		((JComponent) datos).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Datos del Proveedor", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(70, 70, 70)));
		
		int x=30, y=35, al= 30, an=150, an2=180;
		
		Font f = new java.awt.Font("Arial", Font.BOLD, 13);
		
		lblNombre.setBounds(x, y, an, al);
		lblNombre.setFont(f);
		x += an2-30; 
		txtNombre = new JTextField();
		txtNombre.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		lblrif.setBounds(x, y, an, al);
		lblrif.setFont(f);
		x += an2-30; 
		txtrif = new JTextField();
		txtrif.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		lbldireccion.setBounds(x, y, an, al);
		lbldireccion.setFont(f);
		x += an2-30; 
		txtdireccion = new JTextField();
		txtdireccion.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		lbltelefono.setBounds(x, y, an, al);
		lbltelefono.setFont(f);
		x += an2-30;  
		txttelefono = new JTextField();
		txttelefono.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		lblemail.setBounds(x, y, an, al);
		lblemail.setFont(f);
		x += an2-30; 
		txtemail = new JTextField();
		txtemail.setBounds(x, y, an2, al);
		x = 30; y += al+10;
		
		datos.setBounds((xancho/2)-(x+an+an2+30), 35, (x+an+an2+30)*2, y+20);
		datos.add(txtNombre);
		datos.add(txtrif);
		datos.add(txttelefono);
		datos.add(txtemail);
		datos.add(txtdireccion);
		datos.add(lblNombre);
		datos.add(lblrif);
		datos.add(lbltelefono);
		datos.add(lblemail);
		datos.add(lbldireccion);
		formulario.add(datos);
		
		//Panel de Botones
		btnlimpiar = new JButton("Limpiar");
		btnmodificar = new JButton("Modificar");
		btneliminar = new JButton("Eliminar");;
		btnguardar = new JButton("Guardar");;
		btnvolver = new JButton("Volver");;
		
		x=40; y=25; al= 35;
		
		btnvolver.setBounds(x, y, (xancho/5)-20, al);
		x += (xancho/5)-20;
		btnlimpiar.setBounds(x, y, (xancho/5)-20, al);
		x += (xancho/5)-20;
		btnmodificar.setBounds(x, y, (xancho/5)-20, al);
		x += (xancho/5)-20;
		btneliminar.setBounds(x, y, (xancho/5)-20, al);
		x += (xancho/5)-20;
		btnguardar.setBounds(x, y, (xancho/5)-20, al);
		btnlimpiar.setIcon(new ImageIcon("Imagenes/vaciar.png"));
		btnmodificar.setIcon(new ImageIcon("Imagenes/modificar.png"));
		btneliminar.setIcon(new ImageIcon("Imagenes/borrar.png"));
		btnvolver.setIcon(new ImageIcon("Imagenes/retornar.png"));
		btnguardar.setIcon(new ImageIcon("Imagenes/guardar.png"));
		
		
		botones.add(btnvolver);
		botones.add(btnlimpiar);
		botones.add(btnmodificar);
		botones.add(btneliminar);
		botones.add(btnguardar);
		
		formulario.setBounds(10, 30, xancho-20, xalto-120);
		botones.setBounds(10, xalto-80, xancho-20, 73);
		this.add(formulario);
		this.add(botones);
		
		// controladores
		controlador_proveedores e = new controlador_proveedores(this);
		
		btnvolver.addActionListener(e);
		btnlimpiar.addActionListener(e);
		btnmodificar.addActionListener(e);
		btneliminar.addActionListener(e);
		btnguardar.addActionListener(e);
		
		txtNombre.addKeyListener(e);
		
		setBounds(xx, xy, xancho, xalto);
	}
	
	public void limpiar(){
		txtrif.setText("");
		txtdireccion.setText("");
		txttelefono.setText("");
		txtemail.setText("");
		txtNombre.setText("");
	}
}
package Reportes;

import java.text.*;
import java.util.Date;

public class Antiguedad{

	public int getEdad(String xfecha){
		   
	    Date fechaActual = new Date();
	    SimpleDateFormat formato = new SimpleDateFormat("dd/MM/yyyy");
	    String hoy = formato.format(fechaActual);
	    String[] dat1= xfecha.split("/");
	    String[] dat2 = hoy.split("/");
	    int anos = Integer.parseInt(dat2[2]) - Integer.parseInt(dat1[2]);
	    int mes = Integer.parseInt(dat2[1]) - Integer.parseInt(dat1[1]);
	    if (mes < 0) {
	      anos = anos - 1;
	    } else if (mes == 0) {
	    	int dia = Integer.parseInt(dat2[0]) - Integer.parseInt(dat1[0]);
	    	
	      if (dia < 0) {
	        anos = anos - 1;
	      }
	    }
	    return anos;
	  }
}
package Reportes;

import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class Conrolador_reportes implements ActionListener{
	reportes vista;
	
	public Conrolador_reportes(reportes xvista){
		vista = xvista;
	}
	
	public void actionPerformed(ActionEvent e){
		String c = e.getActionCommand();
		
		if(c.equalsIgnoreCase("volver")){
			vista.setVisible(false);
		}
		
		if(c.equalsIgnoreCase("empleados")){
			vista.panel.setVisible(true);
			vista.panel2.setVisible(false);
			vista.panel3.setVisible(false);
			vista.cargar_empleados();
		}
		if(c.equalsIgnoreCase("clientes")){
			vista.panel.setVisible(false);
			vista.panel2.setVisible(true);
			vista.panel3.setVisible(false);
			vista.cargar_clientes();
		}
		if(c.equalsIgnoreCase("proveedores")){
			vista.panel.setVisible(false);
			vista.panel2.setVisible(false);
			vista.panel3.setVisible(true);
			vista.cargar_proveedores();
		}
	}

}
package Reportes;

import java.sql.ResultSet;
import java.sql.SQLException;

import javax.swing.*;
import javax.swing.table.DefaultTableCellRenderer;

import Clientes.MiModelo;
import Conexion.Conexion;
import Panel_Generico.Panel;

public class reportes extends JPanel{
	
	public JButton btnvolver, btnempleados, btnclientes, btnProveedores;
	public String[] ti = {"Cedula","Nombre","Apellido","Sexo","Nacionalidad","Edad","Tiempo"};
	public String[] ti2 = {"Cedula","Nombre","Apellido","Telefono","Dirección"};
	public String[] ti3 = {"Nombre","R.I.F","Telefono","E-mail","Dirección"};
	
	public Panel botones;
	public  JScrollPane panel, panel2, panel3;
	public  MiModelo modelo, modelo2, modelo3;
	public JTable tabla, tabla2, tabla3;
	
	private static final long serialVersionUID = 1L;

	public Conexion con;
	 
	public reportes(int xx, int xy, int xancho, int xalto, Conexion xcon){
		con = xcon;
		this.setLayout(null);
		((JComponent) this).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Reportes", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(255, 255, 255)));
		
		botones = new Panel();
		botones.setLayout(null);
		((JComponent) botones).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Panel de Opciones", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(255, 255, 255)));
		Object[][] ma = new Object[0][ti.length];
		
		Object[] titulos = new Object[ti.length];
		for(int i=0; i<ti.length; i++){
			titulos[i] = ti[i];
		}
		
		modelo = new MiModelo(ma,titulos);
		tabla = new JTable(modelo);
		DefaultTableCellRenderer tcr = new DefaultTableCellRenderer();
		tcr.setHorizontalAlignment(SwingConstants.CENTER);
		for(int i=0; i<titulos.length; i++){
			tabla.getColumnModel().getColumn(i).setCellRenderer(tcr);
		}
		panel = new JScrollPane(tabla);
		panel.setBounds(10, 35, xancho-25, xalto-122);
	
		Object[] t2 = new Object[ti2.length];
		for(int i=0; i<ti2.length; i++){
			t2[i] = ti2[i];
		}
		modelo2 = new MiModelo(ma,t2);
		tabla2 = new JTable(modelo2);
		DefaultTableCellRenderer tcr2 = new DefaultTableCellRenderer();
		tcr2.setHorizontalAlignment(SwingConstants.CENTER);
		for(int i=0; i<t2.length; i++){
			tabla2.getColumnModel().getColumn(i).setCellRenderer(tcr2);
		}
		
		((JComponent) panel).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Empleados", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(255, 255, 255)));
		
		panel2 = new JScrollPane(tabla2);
		panel2.setBounds(10, 35, xancho-25, xalto-122);
		panel2.setVisible(false);
		
		((JComponent) panel2).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Clientes", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(255, 255, 255)));
		
		Object[] t3 = new Object[ti3.length];
		for(int i=0; i<ti3.length; i++){
			t3[i] = ti3[i];
		}
		modelo3 = new MiModelo(ma,t3);
		tabla3 = new JTable(modelo3);
		DefaultTableCellRenderer tcr3 = new DefaultTableCellRenderer();
		tcr3.setHorizontalAlignment(SwingConstants.CENTER);
		for(int i=0; i<t3.length; i++){
			tabla3.getColumnModel().getColumn(i).setCellRenderer(tcr3);
		}
		panel3 = new JScrollPane(tabla3);
		panel3.setBounds(10, 35, xancho-25, xalto-122);
		panel3.setVisible(false);
		
		((JComponent) panel3).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Proveedores", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(255, 255, 255)));
		
		btnempleados = new JButton("Empleados");
		btnclientes = new JButton("Clientes");
		btnProveedores = new JButton("Proveedores");
		btnvolver = new JButton("Volver");
		
		int x=30, y=25, al= 35;
		
		btnvolver.setBounds(x, y, (xancho/4)-20, al);
		x += (xancho/4)-20;
		btnempleados.setBounds(x, y, (xancho/4)-20, al);
		x += (xancho/4)-20;
		btnclientes.setBounds(x, y, (xancho/4)-20, al);
		x += (xancho/4)-20;
		btnProveedores.setBounds(x, y, (xancho/4)-20, al);
		
		Conrolador_reportes e = new Conrolador_reportes(this); 
		
		btnvolver.addActionListener(e);
		btnempleados.addActionListener(e);
		btnclientes.addActionListener(e);
		btnProveedores.addActionListener(e);
		
		btnempleados.setIcon(new ImageIcon("Imagenes/docente.png"));
		btnclientes.setIcon(new ImageIcon("Imagenes/user.png"));
		btnProveedores.setIcon(new ImageIcon("Imagenes/proveedores.jpg"));
		btnvolver.setIcon(new ImageIcon("Imagenes/retornar.png"));
		
		botones.add(btnvolver);
		botones.add(btnempleados);
		botones.add(btnclientes);
		botones.add(btnProveedores);
		cargar_empleados();
		botones.setBounds(10, xalto-80, xancho-20, 73);
		this.add(botones);
		this.setBounds(xx, xy, xancho, xalto);
		this.add(panel);
		this.add(panel2);
		this.add(panel3);
	}
	
	public void cargar_empleados(){
		int x = modelo.getRowCount();
		for(int i=0; i<x; i++){
			modelo.removeRow(0);
		}
		Antiguedad a = new Antiguedad();
		int i=0;
		ResultSet rs;
		rs = con.Consultar("select * from empleados");
		try {
			if(rs != null){
				rs.first();
				  modelo.addRow(new Object[1]);
				  tabla.setValueAt(rs.getObject(1).toString(), i, 0);
				  tabla.setValueAt(rs.getObject(2).toString(), i, 1);
				  tabla.setValueAt(rs.getObject(3).toString(), i, 2);
				  tabla.setValueAt(rs.getObject(4).toString(), i, 3);
				  tabla.setValueAt(rs.getObject(7).toString(), i, 4);
				  tabla.setValueAt(a.getEdad(rs.getObject(5).toString()), i, 5);
				  tabla.setValueAt(a.getEdad(rs.getObject(6).toString()), i, 6);
				  i++;
				while(rs.next()){
					modelo.addRow(new Object[1]);
					  tabla.setValueAt(rs.getObject(1).toString(), i, 0);
					  tabla.setValueAt(rs.getObject(2).toString(), i, 1);
					  tabla.setValueAt(rs.getObject(3).toString(), i, 2);
					  tabla.setValueAt(rs.getObject(4).toString(), i, 3);
					  tabla.setValueAt(rs.getObject(7).toString(), i, 4);
					  tabla.setValueAt(a.getEdad(rs.getObject(5).toString()), i, 5);
					  tabla.setValueAt(a.getEdad(rs.getObject(6).toString()), i, 6);
					i++;
				}
			}
		} catch (SQLException e1){
		}
	}
	
	public void cargar_clientes(){
		int x = modelo2.getRowCount();
		for(int i=0; i<x; i++){
			modelo2.removeRow(0);
		}
		int i=0;
		ResultSet rs;
		rs = con.Consultar("select * from cliente");
		try {
			if(rs != null){
				rs.first();
				  modelo2.addRow(new Object[1]);
				  tabla2.setValueAt(rs.getObject(1).toString(), i, 0);
				  tabla2.setValueAt(rs.getObject(2).toString(), i, 1);
				  tabla2.setValueAt(rs.getObject(3).toString(), i, 2);
				  tabla2.setValueAt(rs.getObject(5).toString(), i, 3);
				  tabla2.setValueAt(rs.getObject(4).toString(), i, 4);
				  i++;
				while(rs.next()){
					modelo2.addRow(new Object[1]);
					  tabla2.setValueAt(rs.getObject(1).toString(), i, 0);
					  tabla2.setValueAt(rs.getObject(2).toString(), i, 1);
					  tabla2.setValueAt(rs.getObject(3).toString(), i, 2);
					  tabla2.setValueAt(rs.getObject(5).toString(), i, 3);
					  tabla2.setValueAt(rs.getObject(4).toString(), i, 4);
					i++;
				}
			}
		} catch (SQLException e1){
		}
	}
	public void cargar_proveedores(){
		int x = modelo3.getRowCount();
		for(int i=0; i<x; i++){
			modelo3.removeRow(0);
		}
		int i=0;
		ResultSet rs;
		rs = con.Consultar("select * from proveedores");
		try {
			if(rs != null){
				rs.first();
				  modelo3.addRow(new Object[1]);
				  tabla3.setValueAt(rs.getObject(1).toString(), i, 0);
				  tabla3.setValueAt(rs.getObject(2).toString(), i, 1);
				  tabla3.setValueAt(rs.getObject(3).toString(), i, 2);
				  tabla3.setValueAt(rs.getObject(4).toString(), i, 3);
				  tabla3.setValueAt(rs.getObject(5).toString(), i, 4);
				  i++;
				while(rs.next()){
					modelo3.addRow(new Object[1]);
					  tabla3.setValueAt(rs.getObject(1).toString(), i, 0);
					  tabla3.setValueAt(rs.getObject(2).toString(), i, 1);
					  tabla3.setValueAt(rs.getObject(3).toString(), i, 2);
					  tabla3.setValueAt(rs.getObject(4).toString(), i, 3);
					  tabla3.setValueAt(rs.getObject(5).toString(), i, 4);
					i++;
				}
			}
		} catch (SQLException e1){
		}
	}
}
package Ventas;

import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.sql.ResultSet;
import java.sql.SQLException;
import javax.swing.*;
import javax.swing.table.DefaultTableCellRenderer;
import Clientes.MiModelo;
import Conexion.Conexion;
import Panel_Generico.Panel;

public class Ventas extends JPanel implements ActionListener{
	
	public JButton btnvolver;
	public String[] ti = {"Codigo","Producto","Cantidad","Precio"};
	public Panel botones;
	public  JScrollPane panel;
	public  MiModelo modelo;
	public JTable tabla;
	
	private static final long serialVersionUID = 1L;

	public Conexion con;
	 
	public Ventas(int xx, int xy, int xancho, int xalto, Conexion xcon){
		con = xcon;
		this.setLayout(null);
		((JComponent) this).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Ventas", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(255, 255, 255)));
		
		botones = new Panel();
		botones.setLayout(null);
		((JComponent) botones).setBorder(
				javax.swing.BorderFactory.createTitledBorder(
						null, "Panel de Opciones", javax.swing.border.
						TitledBorder.DEFAULT_JUSTIFICATION, javax.swing.
						border.TitledBorder.DEFAULT_JUSTIFICATION, new 
						java.awt.Font("Arial", 1, 13), new 
						java.awt.Color(255, 255, 255)));
		Object[][] ma = new Object[0][ti.length];
		
		Object[] titulos = new Object[ti.length];
		for(int i=0; i<ti.length; i++){
			titulos[i] = ti[i];
		}
		
		modelo = new MiModelo(ma,titulos);
		tabla = new JTable(modelo);
		DefaultTableCellRenderer tcr = new DefaultTableCellRenderer();
		tcr.setHorizontalAlignment(SwingConstants.CENTER);
		for(int i=0; i<titulos.length; i++){
			tabla.getColumnModel().getColumn(i).setCellRenderer(tcr);
		}
		panel = new JScrollPane(tabla);
		panel.setBounds(10, 35, xancho-25, xalto-122);
		
		btnvolver = new JButton("Volver");
				
		btnvolver.setBounds(10, 25, (xancho/5)-20, 35);
		
		botones.add(btnvolver);
		botones.setBounds(10, xalto-80, xancho-20, 70);
		
		btnvolver.addActionListener(this);
		btnvolver.setIcon(new ImageIcon("Imagenes/retornar.png"));
		this.add(botones);
		this.setBounds(xx, xy, xancho, xalto);
		this.add(panel);
	}
	
	public void cargar(){
		int x = modelo.getRowCount();
		for(int i=0; i<x; i++){
			modelo.removeRow(0);
		}
		int i=0;
		ResultSet rs;
		rs = con.Consultar("select * from ventas");
		try {
			if(rs != null){
				rs.first();
				  modelo.addRow(new Object[1]);
				  tabla.setValueAt(rs.getObject(2).toString(), i, 0);
				  tabla.setValueAt(rs.getObject(3).toString(), i, 1);
				  tabla.setValueAt(rs.getObject(4).toString(), i, 2);
				  tabla.setValueAt(rs.getObject(5).toString(), i, 3);
				  i++;
				while(rs.next()){
					modelo.addRow(new Object[1]);
					tabla.setValueAt(rs.getObject(2).toString(), i, 0);
					tabla.setValueAt(rs.getObject(3).toString(), i, 1);
					tabla.setValueAt(rs.getObject(4).toString(), i, 2);
					tabla.setValueAt(rs.getObject(5).toString(), i, 3);
					i++;
				}
			}
		} catch (SQLException e1){
		}
	}

	public void actionPerformed(ActionEvent e){ 
		String c = e.getActionCommand();
		if(c.equalsIgnoreCase("volver")){
			this.setVisible(false);
		}
	}
}
