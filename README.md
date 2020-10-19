@GetMapping("/report/{format}")
    public  String generateReport(@PathVariable String format) throws FileNotFoundException, JRException {
        return reportService.exportReport(format);
    }

package com.fjs.inventory.service;

import com.fjs.inventory.dao.UserRepository;
import com.fjs.inventory.entities.User;
import net.sf.jasperreports.engine.*;
import net.sf.jasperreports.engine.data.JRBeanCollectionDataSource;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.util.ResourceUtils;

import java.io.File;
import java.io.FileNotFoundException;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@Service
public class ReportService {
    @Autowired
    private UserRepository userRepository;

    public String exportReport(String reportFormat) throws FileNotFoundException, JRException {
        String path="C:\\Users\\Admin\\Desktop\\TestReport";
        List<User>users= (List<User>) userRepository.findAll();

        //Load file and compile it
        File file= ResourceUtils.getFile("classpath:users.jrxml");
        JasperReport jasperReport= JasperCompileManager.compileReport(file.getAbsolutePath());
        JRBeanCollectionDataSource dataSource=new JRBeanCollectionDataSource(users);
        Map<String,Object> parameters=new HashMap<>();
        JasperPrint jasperPrint= JasperFillManager.fillReport(jasperReport,parameters,dataSource);

        if (reportFormat.equalsIgnoreCase("html")){
            JasperExportManager.exportReportToHtmlFile(jasperPrint,
                    path +"\\users.html");
        }
        if (reportFormat.equalsIgnoreCase("pdf")){
            JasperExportManager.exportReportToPdfFile(jasperPrint,
                    path +"\\users.pdf");
        }
        return "report generated in path : " + path;
    }
}

    
    
