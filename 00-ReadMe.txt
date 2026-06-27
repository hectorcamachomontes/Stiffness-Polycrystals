In order to install Neper, the following three videos may be of help:

https://www.youtube.com/watch?v=Wy9n756wFu4
https://www.youtube.com/watch?v=fkWhzuWM1lE
https://www.youtube.com/watch?v=3ENXVNd5UzI

Before starting working, be sure to add the files: "02_Neper_generator.txt" and "datapoly.txt" that correspond to the number of grains Ngranos = 100 or Ngranos = 892.

The case Ngranos = 892 is taken from Ref: Z. Zaiemyekeh, S. Sayahlatifi, D. L. Romanyk, and J. D. Hogan, Understanding the effect of microstructure on the failure behavior of additively manufactured Al2O3 ceramics: 3D micromechanical modeling, Materials & Design, 244, p. 113167, 2024. https://doi.org/10.1016/j.matdes.2024.113167. We thank the authors for kindly sending the EBSD data to us.

1. The file: "datapoly.txt" has the information on the Euler angles that can be obtained from EBSD or can be constructed based on theoretical assumptions.
"datapoly.txt" contains Ngranos rows, which means that the Representative Volume Element has Ngranos grains or domains. Each row has the three Euler angles that characterize the grain crystal orientation.

2. In the same folder, the files: "datapoly.txt" and "01_Rotacion_de_propiedades.py" must be placed and run "01_Rotacion_de_propiedades.py". The result is the file: "output.txt"
"01_Rotacion_de_propiedades.py" contains the elastic properties (stiffness) for crystal alumina (see Cmat), the input is the file: "datapoly.txt" and the output is the file: "output.txt"
"output.txt" is a file with Ngranos rows, and each row corresponds to the rotated properties of the ith grain. 

3. The file: "02_Neper_generator.txt" contains the distribution size and shape of the polycrystal aggregate. It has the Neper instructions to generate a 3D Representative Volume Element. Neper runs in Ubuntu, be sure about where neper was installed. It could be installed in a virtual machine; in that case, input files must be copied to the virtual machine, and the output files must be copied to the working folder. The output file: zahr.geo

4. The 3D Representative Volume Element generated in Neper is loaded into Gmsh (https://gmsh.info/) to generate the Finite Element Mesh. Open Gmsh with the code: gmsh zahr.geo, then click on Modules -> Mesh ->  3D. After that, go to File -> Export, in Format go to Mesh - Abaqus INP (*.inp) and export as 'gmsh.inp'

5. The file: "03_INP_format_changer.ipynb" deletes all nodes and edges that belong to 1D and 2D elements only leaving all 3D element data. The file: 'gmsh.inp' is the input file and 'mesh_output.inp' is the output file.

6. Open ANSYS Mechanical APDL Product Launcher. Select the working Directory and the Job Name. 

7. The file: "04_INP_to_APDL.txt" converts the Abaqus inp file into ANSYS APDL file. Take the code line in "04_INP_to_APDL.txt" and run it in the command prompt of ANSYS Mechanical. The output file should be: 'file.ans'. In case the output file was 'Job name.ans', rename it as 'file.ans'.

8. The file: "05_assign_MAT_forAPDL.ipynb" adds the ANSYS APDL MAT command to each grain. The input file is: 'file.ans' and the output file is:'apdl_modified.txt'. 

NOTE: the files: "06_porosity_calculatorREV3.txt" and "07_output_porosity_creator.ipynb" are used if porosity is included. In that case, it is necessary to consider extra grains for running "02_Neper_generator.txt". They are those that will play the role of pores.

9. The file: "06_porosity_calculatorREV3.txt" calculates the volume of each grain from smaller to larger grain size in order to detect grains that need to be converted into pores.

10. The file: "07_output_porosity_creator.ipynb" identifies the smaller volumes from the previous step and converts them to pores.

************At this point, the Representative Volume Element is ready for calculation. Next steps are focused on effective properties calculation.****************

11. The file: "08_propiedades.txt" charges the rotated properties into the ANSYS database. The representative Volume Element has Ngranos grains, and Ngranos materials are created in the ANSYS database. Before running "08_propiedades.txt", open the file and update the number of grains (Ngranos).

Note: Each of the following code lists is MAPDL code to be run in ANSYS Mechanical. Follows the these steps for each one:
In Command Prompt:
finish
/clear

File -> Read Input from...
"08_propiedades.txt"
'apdl_modified.txt'
Select -> Everything

Do this for each files of the steps 12 - 20.

Stiffness coefficients are stored in 'stiffness.csv'. Compliance coefficients are stored in 'compliance, csv'. Running the programs in the order presented here permits fabricating clear CSV files.  

12. The file: "09_calculo_C11_C21_C31.txt" contains the code procedures to calculate the effective stiffness coefficients C_11, C_21, and C_31 according to the representative volume element method on displacement boundary conditions (RVEM-DBC) 

13. The file: "10_calculo_C12_C22_C32.txt" contains the code procedures to calculate the effective stiffness coefficients C_12, C_22, and C_32 according to the representative volume element method on displacement boundary conditions (RVEM-DBC)

14. The file: "11_calculo_C13_C23_C33.txt" contains the code procedures to calculate the effective stiffness coefficients C_13, C_23, and C_33 according to the representative volume element method on displacement boundary conditions (RVEM-DBC)

15. The file: "12_calculo_C44.txt" contains the code procedures to calculate the effective stiffness coefficient C_44 according to the representative volume element method (RVEM)

16. The file: "13_calculo_C55.txt" contains the code procedures to calculate the effective stiffness coefficient C_55 according to the representative volume element method (RVEM)

17. The file: "14_calculo_C66.txt" contains the code procedures to calculate the effective stiffness coefficient C_66 according to the representative volume element method (RVEM)

18. The file: "15_Stress-Cond. de frot. para S11_S21_S31.txt" contains the code procedures to calculate the effective compliance coefficients S_11, S_21, and S_31 according to the representative volume element method on displacement boundary conditions (RVEM-SBC) 

19. The file: "16_Stress-Cond. de frot. para S12_S22_S32.txt" contains the code procedures to calculate the effective compliance coefficients S_12, S_22, and S_32 according to the representative volume element method on displacement boundary conditions (RVEM-SBC)

20. The file: "17_Stress-Cond. de frot. para S13_S23_S33.txt" contains the code procedures to calculate the effective compliance coefficients S_13, S_23, and S_33 according to the representative volume element method on displacement boundary conditions (RVEM-SBC)

****** Look for an inverse matrix calculator, find the inverse of the matrix S [i,j = 1,2,3] and compare it with the matrix C [i,j = 1,2,3].*************** 
