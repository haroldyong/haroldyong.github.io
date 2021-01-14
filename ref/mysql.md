
DELIMITER $$  
DROP PROCEDURE IF EXISTS `dbcall`.`get_page`$$  
CREATE DEFINER=`root`@`localhost` PROCEDURE `get_page`(  
/**//*Table name*/  
tableName varchar(100),  
/**//*Fileds to display*/  
fieldsNames varchar(100),  
/**//*Page index*/  
pageIndex int,  
/**//*Page Size*/  
pageSize int,   
/**//*Field to sort*/  
sortName varchar(500),  
/**//*Condition*/  
strWhere varchar(500)  
)  
BEGIN   
DECLARE fieldlist varchar(200);   
if fieldsNames=''||fieldsNames=null THEN  
set fieldlist='*';  
else  
set fieldlist=fieldsNames;   
end if;  
if strWhere=''||strWhere=null then  
if sortName=''||sortName=null then   
set @strSQL=concat('SELECT ',fieldlist,' FROM ',tableName,' LIMIT ',(pageIndex-1)*pageSize,',',pageSize);  
else  
set @strSQL=concat('SELECT ',fieldlist,' FROM ',tableName,' ORDER BY ',sortName,' LIMIT ',(pageIndex-1)*pageSize,',',pageSize);   
end if;  
else  
if sortName=''||sortName=null then  
set @strSQL=concat('SELECT ',fieldlist,' FROM ',tableName,' WHERE ',strWhere,' LIMIT ',(pageIndex-1)*pageSize,',',pageSize);  
else  
set @strSQL=concat('SELECT ',fieldlist,' FROM ',tableName,' WHERE ',strWhere,'
ORDER BY ',sortName,' LIMIT ',(pageIndex-1)*pageSize,',',pageSize);   
end if;  
end if;   
PREPARE stmt1 FROM @strSQL;   
EXECUTE stmt1;  
DEALLOCATE PREPARE stmt1;  
END$$  
DELIMITER ;
