1.在entity和dto里写构造方法

    public class StudentDto {
        private String studentId;
        private String firstName;
        private String lastName;
        private int year;
    
        public StudentDto(Student entity) {
            this.studentId = entity.getStudentId();
            this.firstName = entity.getFirstName();
            this.lastName = entity.getLastName();
            this.year = entity.getYear();
        }
    }
    
2.在entity和dto里写转换方法

    public class StudentDto {
        private String studentId;
        private String firstName;
        private String lastName;
        private int year;
    
        public Student toEntity() {
            Student entity = new Student();
    
            entity.setStudentId(this.studentId);
            entity.setFirstName(this.firstName);
            entity.setLastName(this.lastName);
            entity.setYear(this.year);
            
            return entity;
        }
    }
    
3.专门写一个class，里面写转换方法

    public class StudentMapper {
    
        public StudentDto toDto(Student entity) {
            StudentDto dto = new StudentDto();
            dto.setStudentId(entity.getStudentId());
            dto.setFirstName(entity.getFirstName());
            dto.setLastName(entity.getLastName());
            dto.setYear(entity.getYear());
    
            return dto;
        }
    
        public Student toEntity(StudentDto dto) {
            Student entity = new Student();
            entity.setStudentId(dto.getStudentId());
            entity.setFirstName(dto.getFirstName());
            entity.setLastName(dto.getLastName());
            entity.setYear(dto.getYear());
    
            return entity;
        }
    }
    
4.使用第三方库Model Mapper Library

5.BeanUtils

    import org.springframework.beans.BeanUtils
    ...
    BeanUtils.copyProperties(sourceObject, targetObject);
    
    BeanUtils.copyProperties(sourceObj, targetObj, "propertyToIgnoreA", "propertyToIgnoreB", "propertyToIgnoreC");
    
[link1](https://stackoverflow.com/questions/28703401/conversion-of-dto-to-entity-and-vice-versa/54478261)
[link2](https://www.amitph.com/spring-entity-to-dto/)