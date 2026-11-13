*CRUD com Boostrap, PHP e MySQL*

A maior parte dos sistemas em PHP é feito com um banco de dados no back end, e normalmente, esse banco de dados é o MySQL. Por isso, é importante criar essa camada de acesso a dados de uma forma bem genérica e independente, para que depois ela possa ser reaproveitada em todo o sistema.
Neste tutorial, você vai ver como deve ser esse banco de dados para o nosso sistema de CRUD e como criá-lo, executando o SQL pelo phpMyAdmin. Depois disso, vamos criar um arquivo de configurações para usar em todo o sistema. E, por fim, vamos criar o script de conexão com esse banco de dados, usando PHP.
Passo 1: Crie as seguintes tabelas no banco de dados

Para este tutorial, vamos criar um banco de dados bem simples, com algumas tabelas, conforme o modelo:
<img width="962" height="548" alt="image" src="https://github.com/user-attachments/assets/001a86bb-354f-4e5d-bdb3-fa6390fb0793" />


Passo 2: Crie o Arquivo de Configurações do Sistema

Agora, vamos criar um arquivo de configurações, que vai guardar todos os dados que vão ser usados em todos os outros scripts PHP do sistema.

Crie um arquivo chamado config.php, na pasta principal, e coloque o código a seguir:
<img width="485" height="529" alt="image" src="https://github.com/user-attachments/assets/dd54d941-fb88-4cc1-bf95-5bab693253f1" />

Passo 3: Implemente o script de Conexão com o Banco de Dados

Vamos criar um arquivo que vai ter todas as funções de acesso ao banco de dados. Assim, podemos reaproveitar código nas outras partes do CRUD.

Crie um arquivo chamado database.php, na pasta inc do seu projeto, e coloque o código a seguir:
<img width="577" height="359" alt="image" src="https://github.com/user-attachments/assets/f126226e-6309-4351-8769-81f146dea591" />



<?php
        /**
       *  Pesquisa Todos os Registros de uma Tabela
       */
 
       function find_all( $table ) {
           return find($table);
       }
 ?>

<?php

        function save($table = null, $data = null) {
             $database = open_database();
             $columns = null;
             $values = null;
             //print_r($data);

        foreach ($data as $key => $value) {
             $columns .= trim($key, "'") . ",";
             $values .= "'$value',";
        }

       // remove a ultima virgula
             $columns = rtrim($columns, ',');
             $values = rtrim($values, ',');
  
             $sql = "INSERT INTO " . $table . "($columns)" . " VALUES " . "($values);";
             try {
                  $database->query($sql);
                  $_SESSION['message'] = 'Registro cadastrado com sucesso.';
                  $_SESSION['type'] = 'success';
  
             } catch (Exception $e) { 
  
                  $_SESSION['message'] = 'Nao foi possivel realizar a operacao.';
                  $_SESSION['type'] = 'danger';
             } 
             close_database($database);
        } 
      ?>

<?php

function update($table = null, $id = 0, $data = null) {
  $database = open_database();
  $items = null;
  foreach ($data as $key => $value) {
    $items .= trim($key, "'") . "='$value',";
  }
  // remove a ultima virgula
  $items = rtrim($items, ',');
  $sql  = "UPDATE " . $table;
  $sql .= " SET $items";
  $sql .= " WHERE id=" . $id . ";";
  try {
    $database->query($sql);
    $_SESSION['message'] = 'Registro atualizado com sucesso.';
    $_SESSION['type'] = 'success';
  } catch (Exception $e) { 
    $_SESSION['message'] = 'Nao foi possivel realizar a operacao.';
    $_SESSION['type'] = 'danger';
  } 
  close_database($database);
}
?>

<?php

function remove( $table = null, $id = null ) {
     $database = open_database();
	
     try {
          if ($id) {
               $sql = "DELETE FROM " . $table . " WHERE id = " . $id;
               $result = $database->query($sql);
           if ($result = $database->query($sql)) {   	
                $_SESSION['message'] = "Registro Removido com Sucesso.";
                $_SESSION['type'] = 'success';
           }
       }
    }    catch (Exception $e) { 
            $_SESSION['message'] = $e->GetMessage();
            $_SESSION['type'] = 'danger';
    }
    close_database($database);
}
?>
		
Este será um arquivo de funções do banco de dados. Tudo que for relativo ao BD estará nele.

Vamos entender esses códigos…

Primeiramente, na linha 3, configuramos o MySQL para avisar sobre erros graves, usando a função mysqli_report(). Depois, temos duas funções.

A primeira função – open_database() – abre a conexão com a base de dados, e retorna a conexão realizada, se der tudo certo. Se houver algum erro, a função dispara uma exceção, que pode ser exibida ao usuário.

Já a segunda função – close_database($conn) – fecha a conexão que for passada. Se houver qualquer erro, a função dispara uma exceção, também.

Passo 4: Teste a Conexão

Agora sim, vamos ver se o banco de dados está conectado ao CRUD.

Crie um arquivo chamado index.php, na pasta principal, e coloque o código a seguir:

<?php require_once 'config.php'; ?>
<?php require_once DBAPI; ?>

<?php
	$db = open_database(); 
	
	if ($db) {
		echo '<h1>Banco de Dados Conectado!</h1>';
	} else {
		echo '<h1>ERRO: Não foi possível Conectar!</h1>';
	}
?>
